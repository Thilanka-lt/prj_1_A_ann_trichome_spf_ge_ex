# Mapping reads
   
   
### 1. Create a genome index file
 Indices allow the bowtie aligner to narrow down the potential origin of a query sequence within the genome, saving both time and memory.

```
module load GCC/7.3.0-2.30  OpenMPI/3.1.1
module load Bowtie2/2.3.4.2
bowtie2-build GCA_003112345.1_ASM311234v1_genomic.fa GCA_003112345.1_ASM311234v1_genomic

```

