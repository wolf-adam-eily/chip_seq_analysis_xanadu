# chip_seq_analysis_xanadu
This repository is a usable, publicly available ChIP-Seq analysis tutorial.
All steps have been provided for the UConn CBC Xanadu cluster here with appropriate headers for the Slurm scheduler that can be modified simply to run.  Commands should never be executed on the submit nodes of any HPC machine.  If working on the Xanadu cluster, you should use sbatch scriptname after modifying the script for each stage.  Basic editing of all scripts can be performed on the server with tools, such as nano, vim, or emacs.  If you are new to Linux, please use <a href="https://bioinformatics.uconn.edu/unix-basics/">this</a> handy guide for the operating system commands.  In this guide, you will be working with common bioinformatic file formats, such as <a href="https://en.wikipedia.org/wiki/FASTA_format">FASTA</a>, <a href="https://en.wikipedia.org/wiki/FASTQ_format">FASTQ</a>, <a href="https://en.wikipedia.org/wiki/SAM_(file_format)">SAM/BAM</a>, and <a href="https://en.wikipedia.org/wiki/General_feature_format">GFF3/GTF</a>. You can learn even more about each file format <a href="https://bioinformatics.uconn.edu/resources-and-events/tutorials/file-formats-tutorial/">here</a>. If you do not have a Xanadu account and are an affiliate of UConn/UCHC, please apply for one <a href="https://bioinformatics.uconn.edu/contact-us/">here</a>.
<div id="toc_container">
<p class="toc_title">Contents</p>
<ul class="toc_list">
<li><a href="#First_Point_Header">1 Overview</>
<li><a href="#Second_Point_Header">2 Accessing the data using sra-toolkit</a></li>
<li><a href="#Third_Point_Header">3 Quality control using sickle</a></li>
<li><a href="#Fourth_Point_Header">4 Aligning reads to a genome using hisat2</a></li>
<li><a href="#Fifth_Point_Header"<5 Peak Calling with MACS2</a></li>
<li><a href="#Sixth_Point_Header">6 Generating total read counts from alignment using htseq-count</a></li>
<li><a href="#Citation">Citations</a></li>
</ul>
</div>
<h2 id="First_Point_Header">Overview</h2>
For this tutorial we will be using the ChIP-Seq data from GEO accession: GSE59530. The objective of the study was to establish the effects of the FOXA1 transcription factor on the binding behaviors of estrogen receptor (ER) in human breast-cancer cells. The experiment made use of small-interfering RNAs (siRNAS) to perform controlled gene knock-outs, then quantifying the ER binding behaviors through ChIP-Sequencing. We will be analyzing a subset of the gene knock-outs, namely the data for a control siRNA (no knock-out) and a FOXA1-siRNA (FOXA1 knock-out). Our objective is to determine the effects of the FOXA1 gene on ER binding behavior in human breast-cancer cells.

For those unfamiliar with the process of ChIP-Sequencing, I advise you to scroll this the EBI's <a href="https://www.ebi.ac.uk/training/online/sites/ebi.ac.uk.training.online/files/user/18/private/chipseq_loos.pdf">ChIP-Seq powerpoint</a>.
