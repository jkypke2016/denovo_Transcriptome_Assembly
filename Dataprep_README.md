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

Trinity requires `.fastq` files (gzipped files `.fastq.gz` are okay as well) for the assembly. However, it is picky when it comes to the information contained in the header of your fastq files and the way how the header was formatted. Starting with raw reads from, e.g. an Illumina run, this should not be a problem but if the primary material is an SRA study then make sure you check the headers are okay.  

##### Converting SRA to FASTQ
SRA files can easily be converted to fastq files. All you need is `sra-tools` which you can get like so:  
```javascript
git clone https://github.com/ncbi/sra-tools
```
After downloading, go to the directory that contains the executable for sra-tools and run `./configure`, then `make` to install. To convert SRAs to FASTQs you need `fastq-dump`, which is part of `sra-tools`. Find the directory that contains the fastq-dump executable and copy the path of this directory into your home `.bashrc` file:
```javascript
    export PATH=/home/usr/ncbi-outdir/sra-tools/linux/gcc/x86_64/rel/bin:$PATH  
    bash # restarts your shell & makes changes active  
```
Now you are ready to **convert a bunch of SRA files into FASTQ files** using the `SRAtoFASTQ.sh` script:  
```javascript
#!/bin/sh

for i in *.sra
do
 fastq-dump --split-3 $i &
 echo "Now converting file $i"
 sleep 3m
done
```
##### Prepping FASTQ files obtained from SRAs for Trinity   
Sometimes SRA files have additional information that is redudant for trinity and needs to be removed or else trinity will show you an error. In my case the header started with the name of the seqencing run (SRR...) and the information on the read length (length=100). If those are removed it will work just fine. The following script called  `prep_fastq_for_trinity.sh` does the job.
```javascript
#!/bin/bash 

for i in *R1.fastq
do
  TAG=${i%%.R1.fastq}
  awk '{if (NR%4==1) {print "@"$2} else if (NR%4==3) {print "+"} else {print}}' $i > $TAG.prepped.R1.fastq & 
  echo "Done processing $i..." 
done
wait 

for i in *R2.fastq
do
  TAG=${i%%.R2.fastq}
  awk '{if (NR%4==1) {print "@"$2} else if (NR%4==3) {print "+"} else {print}}' $i > $TAG.prepped.R2.fastq &
  echo "Done processing $i..." 
done 
wait 
```
After running this cript, make sure you check that all headers were converted correctly to avoid trinity to fail and not knowing why. Do this you run the follwoing caommnds:  


If you have several read files belonging to one organism = one Transcriptome that you want to assemble, then you can concatenate the files to run them in one go with Trinity. You can do this using `nohup zcat *.R1.fastq.gz > All_files.R1.fastq &` to zip-conctenate all R1 (e.g. forward) read files. Do the same for all R2 (e.g. reverse) read files.  As they will probably be huge files, you might want to concatenate them again to save disk space.


### After successful assembly & quality check
 


## *De-novo* assembly using SOAPdenovo-trans
 
