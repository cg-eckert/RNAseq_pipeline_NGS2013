RNAseq_pipeline_NGS2013
=======================

README file / Tutorial

RNAseq - From QC passed paired-end reads to read counts per transcript
Allison, Diego Lauren, Rachel
MSU NGS2013

Please run each command replacing the <   > parts with your files or paths to files
Feel free to look inside each shell script to figure out what they are doing!

You may want to run this pipeline with some sample data.
Here is the link to some sample RNAseq paired end reads (4 samples, 10k for each pair)
	
	curl -O https://www.dropbox.com/sh/k2fnubss7qd3ztj/yTEipf-NcJ

1- Create a working directory

	mkdir -p /mnt/pipeline/
	cd /mnt/pipeline/

2- Clone the scripts into this directory

	git clone https://github.com/mazzottidr/RNAseq_pipeline_NGS2013.git
	cd /mnt/pipeline/RNAseq_pipeline_NGS2013/

3- run INSTALL_RNAseq_group.sh (This will install all required software)

	bash INSTALL_RNAseq_group.sh

4- Download your reference transcriptome

	curl -O <url_to_your_reference_transcriptome>
	gunzip <reference.fasta.gz>

5- Run PREPAREFILES_RNAseq_group.sh (This will prepare the reference transcriptome file)

	bash PREPAREFILES_RNAseq_group.sh <reference.fasta>
	
6- Create folders for each sample to be proccessed (example for 4 samples is shown below).

	mkdir sample1
	mkdir sample2
	mkdir sample3
	mkdir sample4

7- Run PIPELINE_RNAseq_group.sh in parallel for each sample (run in different screens if you have a large dataset)

	cd sample1
	bash ../PIPELINE_RNAseq_group.sh ../<reference.fasta> <path_to_sample1_paired_end1.fq> <path_to_sample1_paired_end2.fq> <sample1_output_file_prefix>

	cd sample2
	bash ../PIPELINE_RNAseq_group.sh ../<reference.fasta> <path_to_sample2_paired_end1.fq> <path_to_sample2_paired_end2.fq> <sample2_output_file_prefix>

	cd sample3
	bash ../PIPELINE_RNAseq_group.sh ../<reference.fasta> <path_to_sample3_paired_end1.fq> <path_to_sample3_paired_end2.fq> <sample3_output_file_prefix>

	cd sample4
	bash ../PIPELINE_RNAseq_group.sh ../<reference.fasta> <path_to_sample4_paired_end1.fq> <path_to_sample4_paired_end2.fq> <sample4_output_file_prefix>


#################################################################################
##### Automation of steps 6 and 7 for N samples will be developed (someday) #####
#####     My shell script skills does not allow me to do this right now!    #####
#################################################################################

8- When step 7 is done for all the samples, run bedtools to create a file that contains read count for each transcript for all bam file

	cd ../
	bedtools multicov -q 30 -p -bams sample1/<sample1_output_file_prefix.bam> sample2/<sample2_output_file_prefix.bam> sample3/<sample3_output_file_prefix.bam> sample4/<sample4_output_file_prefix.bam> -bed <reference.fasta.bed> > bwa_transcriptome_counts.txt

9- DONE! (You may want to run edgeR with the results of this pipeline)
