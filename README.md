# *De-novo* Transcriptome Assembly

**This repository contains all steps (including scripts) needed to de-novo assemble transcriptomes from raw reads.**
 
## Overview
 
 Two different _de-novo_ transcriptome assemblers are compared:
 * [Trinity] (https://github.com/trinityrnaseq/trinityrnaseq/wiki) 
 * [SOAPdenovo-Trans] (https://sourceforge.net/projects/soapdenovotrans/files/SOAPdenovo-Trans/)    

Raw reads are assembled with and without using available trimming options.  
Quality checks are performed after successful assembly.  

All scripts are made for the type of data I am working with. My primary material is RNA-seq data, PolyA paired-end reads from Illumina. All samples (read files) are named systematically, so that scripts can be written that are able to process a lot of files simultaneously/ successively.  Each sample starts with it's name which is a unique identifier, which will be called **TAG** in the scripts, then another identifier, **R1 or R2**, which are forward and reverse strands respectively. Other endings denote the file type , e.g. `.fastq `.  

## *De-novo* assembly using Trinity

### Preparation of the data before assembly

### Running Trinity

### Performing quality checks

### After successful assembly & quality checks
 
## *De-novo* assembly using SOAPdenovo-trans
