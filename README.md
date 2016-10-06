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

##### Things to be aware of
  * If you run out of HDD space  on your server, the assembly job will just fail - even if it was running for 3 days already. So **check how much free space is available before running a job**. You can do this using `df -h`. 
  * It is natural with RNA-seq data that the coverage of some reads will vastly exceed that of others, simply because at any point in time some genes are more expressed than others leading to a very uneven coverage  of reads after sequencing. To balance this out a little bit it is suggested to **use the nomalization function (`--normalize_reads`)** in Trinity which reduces any read coverage to a maximum of 50x.
  * Be aware of the amount of **CPUs** you can use (I am running everything on 16 CPUs)
  * Trinity will even assemble your paired-end reads if you don't know their strandedness and you be aware that **treating your reads as non-stranded is the deafult setting**. So if your data is stranded don't forget to include the setting `--SS_lib_type`.  

### Running Trinity

### Performing quality checks

### After successful assembly & quality checks
 
## *De-novo* assembly using SOAPdenovo-trans
