# *De-novo* Transcriptome Assembly

 **This repository contains all steps (including scripts) needed to de-novo assemble transcriptomes from raw reads.**
 
## Overview
 
 Two different _de-novo_ transcriptome assemblers are compared:
 * [Trinity] (https://github.com/trinityrnaseq/trinityrnaseq/wiki) 
 * [SOAPdenovo-Trans] (https://sourceforge.net/projects/soapdenovotrans/files/SOAPdenovo-Trans/)    

Raw reads are assembled with and without using available trimming options.  
Quality checks are performed after successful assembly.  
  
## *De-novo* assembly using Trinity

### After successful assembly & quality check

Trinty suggests to check a few things to get a better idea on **how many expressed genes/ transcripts are really there**. This information is taken from the [Trinity] (https://github.com/trinityrnaseq/trinityrnaseq/wiki/Trinity-FAQ#ques_sra_fq_conversion) page.
 * *Examine counts of transcripts/ genes vs. the minimum expression thresholds*. This lets you count the number that reflect the majority of reads (even with little read support). Further instructions on how to do this can be found [here] (https://github.com/trinityrnaseq/trinityrnaseq/wiki/Trinity-Transcript-Quantification)
	* Entries with >= ~1 fpkm / tpm tend to be heavily enriched for transcripts and would typically be annotated in a genome annotation
	* Calculate the contig Ex90N50 value
	  * contig N50: is computed based on transcript contigs representing the top 90% of expressed transcripts
		 * Ex90 count of transcripts: is the number of that represent this top 90% of expression data
   * Click [here] (https://github.com/trinityrnaseq/trinityrnaseq/wiki/Transcriptome-Contig-Nx-and-ExN50-stats) for more information on contig Ex90N50 value  




 
## *De-novo* assembly using SOAPdenovo-trans
 
