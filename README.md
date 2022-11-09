# somatic_variant_calling_gatk_Mutect2
Somatic variant calling using Mutect2


1) Downloading the ref genome

```shell
wget https://hgdownload.soe.ucsc.edu/goldenPath/hg19/bigZips/hg19.fa.gz
gunzip hg19.fa.gz
```
2) Making index and dictionary for ref genome 

```shell
module load samtools gatk picard/2.26.3
samtools faidx hg19.fa

java -jar $EBROOTPICARD/picard.jar CreateSequenceDictionary \
      R=hg19.fa \
      O=hg19.dict
      
samtools index CG22-051-1290-CG22-Run5-15_S15.hardclipped.bam

# to avoid empty sample error by mutect2:

samtools addreplacerg -r '@RG\tID:CG22-051-1290-CG22-Run5-15_S15\tSM:CG22-051-1290-CG22-Run5-15_S15' CG22-051-1290-CG22-Run5-15_S15.hardclipped.bam -o output.bam

```

3) Variant calling

```shell
salloc --time=5:0:0 --ntasks=2 --cpus-per-task=12 --mem-per-cpu=50 --account=def-gooding-ab

gatk Mutect2 -R hg19.fa -I output.bam -O unfiltered.vcf
gatk FilterMutectCalls -R hg19.fa -V unfiltered.vcf -O mutect2_filtered.vcf -f-score-beta 1.5