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


If you have several read files belonging to one organism = one Transcriptome that you want to assemble, then you can concatenate the files to run them in one go with Trinity. You can do this using `nohup zcat *.R1.fastq.gz > All_files.R1.fastq &` to zip-conctenate all R1 (e.g. forward) read files. Do the same for all R2 (e.g. reverse) read files.  As they will probably be huge files, you might want to zip them again to save disk space.

##### Things to be aware of
  * If you run out of HDD space  on your server, the assembly job will just fail - even if it was running for 3 days already. So **check how much free space is available before running a job**. You can do this using `df -h`. 
  * It is natural with RNA-seq data that the coverage of some reads will vastly exceed that of others, simply because at any point in time some genes are more expressed than others leading to a very uneven coverage  of reads after sequencing. To balance this out a little bit it is suggested to **use the nomalization function (`--normalize_reads`)** in Trinity which reduces any read coverage to a maximum of 50x.
  * Be aware of the amount of **CPUs** you can use (I am running everything on 16 CPUs)
  * Trinity will even assemble your paired-end reads if you don't know their strandedness and you be aware that **treating your reads as non-stranded is the deafult setting**. So if your data is stranded don't forget to include the setting `--SS_lib_type`.  

### Running Trinity
You can run Trinity directly from the terminal, however I prefer to put all commands that run Trinity into a script so that I can save it and will not forget what I did (*i.e.* the functions I used and how I set those parameters). Here is a typical script as I use it, called `run_trinity.sh`:  

```javascript
#!/bin/bash 

READ1=$1 # archived reads are OK 
READ2=$2
CPUS=$3 # e.g. 16
MEM=$4 # use G notation, e.g. 200G
TAG=$5
STRAND=$6 # RF (read1:reverse, read2:forward); FR (read1:forward, read2:reverse); 
          # default is treating reads as non-stranded! 

nohup Trinity --seqType fq --normalize_reads --SS_lib_type $STRAND --max_memory $MEM /
--left $READ1 --right $READ2 --CPU $CPUS --output ${TAG}_trinity >& $TAG.trinity.log &
```  
Using aliases (`$1-$5`) you can use this script everytime you assemble a transcriptome without having to adjust the script before running it on a new data set. All you have to do is to go to the directory that contains the read files you would like to assemble and to specify what $1-$5 stand for. Then you can run the script from the terminal, which you do like so:  
`nohup ./run_trinity.sh myspecies.R1.fastq.gz myspecies.R2.fastq.gz 16 200G myspecies RF &`  
To run the script in the background, so that the terminal can still be used, you can 'frame' any command with `nohup <yourcommand> &`. Typing the name of the script is enough to execute it (provided that the script has been made executable). Notice that you should specify the path to the script in case it is not in the same directory as your data. Then you specify what `$1-$5` stand for, separating each input with a single space.  
Trinity will save its output in a separate folder in the current directoy, which will be named `<TAG>_trinity` and it will also create a log file (`<TAG>.trinity.log`) which contains information on all the steps of the assembly.

### Performing quality checks

### After successful assembly & quality checks
 
## *De-novo* assembly using SOAPdenovo-trans
