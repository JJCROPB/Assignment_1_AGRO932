###### agro932juan
###### This is a project that aims to perform a simulation of Next Generation Sequencing dataset in two populations and do population differentiation test. 
###### -For that I used the reference genome of Escherichia coli strain J53 plasmid pOX38-Gen, complete sequence https://www.ncbi.nlm.nih.gov/nuccore/NZ_MF370216.1 with 59705 bp
###### -I used wgsim software https://github.com/lh3/wgsim to run the simulation
###### -First, I create this repository including some directories to organize the files
###### -Then, I cloned this repository in CRANE, a high performance cluster from UNL
###### -After that, I cloned and instaled the software wgsim into the repository in CRANE https://github.com/lh3/wgsim
###### -Once it was cloned, I downloaded the reference genome with 1491 bp and place it into the repository in CRANE https://www.ncbi.nlm.nih.gov/nuccore/NR_041696.1
###### -With the reference genome and the software installed, I ran this code to perform the data simulation using wgsim ecoli.fasta -N 100 -1 60 -2 60 -r 0.1 -e 0 \ -R 0 -X 0 -S 1000000 doc/l1.R1.fq doc/l1.R2.fq
###### -After getting this, I ran a simulation for 10 individuals using this code with r 0.01: for i in {1..10}
do
   wgsim salmon.fasta -N 1000 -1 100 -2 100 -r 0.01 -e 0 -R 0 -X 0 l$i.R1.fq l$i.R2.fq
done
##### -Then I ran a simulation for 10 individuals using this code with r 0.2: for i in {1..10}
do
   wgsim salmon.fasta -N 1000 -1 100 -2 100 -r 0.2 -e 0 -R 0 -X 0 l$i.R1.fq l$i.R2.fq

###### -After getting the Fastq files that contained the sequencing data, I used bwa, samtools and bcftools softwares to perform the alignment to the reference genome and at the end, get BAM files which are compressed binary version of SAM files, generated with the mapping programs. I used this code:
###### module load bwa samtools bcftools
###### bwa index salmon.fasta
###### bwa mem salmon.fasta doc/l1.R1.fq doc/l1.R2.fq | samtools view -bSh - > data/l1.bam
###### -Then I did the alignment for the 10 individuals using this code:
###### for i in {1..10}; do bwa mem salmon.fasta doc/l$i.R1.fq doc/l$i.R2.fq | samtools view -bSh - > data/l$i.bam; done
for i in *.bam; do samtools sort $i -o sorted_$i; done
for i in sorted*.bam; do samtools index $i; done
###### -After doing the alignment, I proceeded to to the SNP calling utilizing samtools, a variant calling software. Fisrt I converted the BAM files to a BCF file called myraw.bcf
###### Samtools faidx salmon.fasta
samtools mpileup -g -f salmon.fasta sorted_l1.bam sorted_l2.bam sorted_l3.bam sorted_l4.bam sorted_l5.bam sorted_l6.bam sorted_l7.bam sorted_l8.bam sorted_l9.bam sorted_l10.bam> data/myraw.bcf
###### -After that, I did the SNP call unsing bcftools:
###### bcftools call myraw.bcf -cv -Ob -o largedata/snps.bcf
###### -Later, I extract the allele frequency at each position using bcftools in a file called frq.txt: 
###### bcftools query -f '%CHROM %POS %AF1\n' largedata/snps.bcf > largedata/frq.txt
###### -Then, I opened R to perform all calculation for fst and generate plots in pdf (check r file assignment1jd in graphs folder)
###### 


