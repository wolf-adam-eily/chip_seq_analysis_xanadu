# chip_seq_analysis_xanadu
This repository is a usable, publicly available ChIP-Seq analysis tutorial.
All steps have been provided for the UConn CBC Xanadu cluster here with appropriate headers for the Slurm scheduler that can be modified simply to run.  Commands should never be executed on the submit nodes of any HPC machine.  If working on the Xanadu cluster, you should use sbatch scriptname after modifying the script for each stage.  Basic editing of all scripts can be performed on the server with tools, such as nano, vim, or emacs.  If you are new to Linux, please use <a href="https://bioinformatics.uconn.edu/unix-basics/">this</a> handy guide for the operating system commands.  In this guide, you will be working with common bioinformatic file formats, such as <a href="https://en.wikipedia.org/wiki/FASTA_format">FASTA</a>, <a href="https://en.wikipedia.org/wiki/FASTQ_format">FASTQ</a>, <a href="https://en.wikipedia.org/wiki/SAM_(file_format)">SAM/BAM</a>, and <a href="https://en.wikipedia.org/wiki/General_feature_format">GFF3/GTF</a>. You can learn even more about each file format <a href="https://bioinformatics.uconn.edu/resources-and-events/tutorials/file-formats-tutorial/">here</a>. If you do not have a Xanadu account and are an affiliate of UConn/UCHC, please apply for one <a href="https://bioinformatics.uconn.edu/contact-us/">here</a>.
<div id="toc_container">
<p class="toc_title">Contents</p>
<ul class="toc_list">
  <li><a href="#First_Point_Header">1 Overview</a></li>
<li><a href="#Second_Point_Header">2 Accessing the data using sra-toolkit</a></li>
<li><a href="#Third_Point_Header">3 Quality control using sickle</a></li>
<li><a href="#Fourth_Point_Header">4 Aligning reads to a genome using hisat2</a></li>
<li><a href="#Fifth_Point_Header"<5 Peak Calling with MACS2</a></li>
<li><a href="#Sixth_Point_Header">6 Generating total read counts from alignment using htseq-count</a></li>
<li><a href="#Citation">Citations</a></li>
</ul>
</div>
<h2 id="First_Point_Header">Overview</h2>
For this tutorial we will be using the ChIP-Seq data from GEO accession: <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi">GSE59530</a>. The objective of the study was to establish the effects of the FOXA1 transcription factor on the binding behaviors of estrogen receptor (ER) in human breast-cancer cells. The experiment made use of small-interfering RNAs (siRNAS) to perform controlled gene knock-outs, then quantifying the ER binding behaviors through ChIP-Sequencing. We will be analyzing a subset of the gene knock-outs, namely the data for a control siRNA (no knock-out) and a FOXA1-siRNA (FOXA1 knock-out). Our objective is to determine the effects of the FOXA1 gene on ER binding behavior in human breast-cancer cells.<br>
<br>
For those unfamiliar with the process of ChIP-Sequencing, I advise you to scroll this <a href="https://www.ebi.ac.uk/training/online/sites/ebi.ac.uk.training.online/files/user/18/private/chipseq_loos.pdf">ChIP-Seq powerpoint</a> from the EBI.<br>

Before beginning, it is important that after connecting via SSH the directory is set to

<pre style="color: silver; background: black;">cd /home/CAM/$your.user.name</pre> 

first. Your home directory contains 10TB of storage and will not pollute the capacities of other users on the cluster. 

The workflow may be cloned into the appropriate directory using the terminal command:
<pre style="color: silver; background: black;">$git clone https://github.com/wolf-adam-eily/refseq_diffexp_funct_annotation_uconn.git
$cd refseq_diffexp_funct_annotation_uconn
$ls  </pre>

<h2 id="Second_Point_Header">Accessing the data using the sra-toolkit/h2>

We will be downloading our data from the sequence-read-archives (SRA), a comprehensive collection of sequenced genetic data submitted to the NCBI by experimenters. The beauty of the SRA is the ease with which genetic data becomes accessible to any scientist with an internet connection, available for download in a variety of formats. Each run in the SRA has a unique identifier. The run may be downloaded using a module of software called the "sratoolkit" and its unique identifier. There are a variety of commands in the sratoolkit, which I invite you to investigate for yourself at https://www.ncbi.nlm.nih.gov/books/NBK158900/.

The data may be accessed at the following web page: 
https://www.ncbi.nlm.nih.gov/sra?term=SRP044607<br>
siCTRL: SRR1635470, SRR1635469, SRR1635468, SRR1635467
siFOXA1: SRR1635474, SRR1635473, SRR1635472, SRR1635471

and downloaded with the sra-toolkit command-line software.
<br>
However, unlike a local terminal, commands in Xanadu must be concatenated into a single script with the required arguments for the Slurm scheduler. Slurm is an free, open-source software for coordinating user-submitted tasks on clusters and servers. When a user submits commands to Slurm, it "schedules" the commands in a queue for the desired processeors, cores, etc. We submit our Slurm scripts to the scheduler queue using the 'sbatc' command. To initialize a script in Unix we use the nano command, which will produce the following window:
<pre style="color: silver; background: black;">
nano data_dump.sh
 GNU nano 2.3.1            File: data_dump.sh                                








				[ New File ] 
^G Get Help  ^O WriteOut  ^R Read File ^Y Prev Page ^K Cut Text  ^C Cur Pos
^X Exit      ^J Justify   ^W Where Is  ^V Next Page ^U UnCut Text^T To Spell</pre>

We now write our script with the appropriate <a href="https://bioinformatics.uconn.edu/resources-and-events/tutorials/xanadu/#Xanadu_6">Slurm arguments</a> followed by our commands:

<pre style="color: silver; background: black;">  GNU nano 2.3.1          File: data_dump.sh                  Modified  
#!/bin/bash
#!/bin/bash
#SBATCH --job-name=data_dump
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -c 10
#SBATCH --partition=general
#SBATCH --mail-type=END
#SBATCH --mail-user=wolf.adam.eily@gmail.com
#SBATCH --mem=64G
#SBATCH -o chipseq_data_download_%j.out
#SBATCH -e chipseq_data_download_%j.err
module load sratoolkit
fastq-dump SRR1635474
fastq-dump SRR1635473
fastq-dump SRR1635472
fastq-dump SRR1635471
fastq-dump SRR1635470
fastq-dump SRR1635469
fastq-dump SRR1635468
fastq-dump SRR1635467
^G Get Help  ^O WriteOut  ^R Read File ^Y Prev Page ^K Cut Text  ^C Cur Pos
^X Exit      ^J Justify   ^W Where Is  ^V Next Page ^U UnCut Text ^T To Spell</pre>

We now press CTRL+X which will ask us if we wish to save, simply type "y" to confirm that we do want to save. Next we will be prompted with the file name, we simply hit enter here to save our file (or, if you like, you may change the file name). Now that we have our script, we may run it with the command:

<pre style="color: silver; background: black;">sbatch fastq_dumps.sh</pre>

It is advised that you familiarize yourself with the arguments for the Slurm scheduler. While it may seem as though running your commands locally will be more efficient due to the hassle of not initializing and writing scripts, do not fall for that trap! The capacity of the Slurm scheduler far exceeds the quickness of entering the commands locally. While the rest of this tutorial will not include the process of initializing and writing the Slurm arguments in a script in its coding, know that the Xanadu scripts in the cloned directory <i>do</i> contain the Slurm arguments. However, before running any cloned Xanadu script, you must "nano" and enter your appropriate email address!

Unless authorized, you cannot add any packages or software to Xanadu. However, you can see all of the software pre-loaded with the following command:
<pre style="color: silver; background: black;">module avail</pre>

Because of this, it is important to manually load modules to be used in the Xanadu bash in every Slurm script.
