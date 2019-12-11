# Mapping reads
   
   
### 1. Create a genome index file
 Indices allow the bowtie aligner to narrow down the potential origin of a query sequence within the genome, saving both time and memory.

```
module load GCC/7.3.0-2.30  OpenMPI/3.1.1
module load Bowtie2/2.3.4.2
bowtie2-build GCA_003112345.1_ASM311234v1_genomic.fa GCA_003112345.1_ASM311234v1_genomic

```
* Four index files were created with the neame *GCA_003112345.1_ASM311234v1_genomic.1.bt2 .....GCA_003112345.1_ASM311234v1_genomic.4.bt2* in */mnt/home/ranawee1/01_A_annua_trichome/differential_expression*

### 2. Read qulity check before trimming
It is essential to see the qulity of RNA seq files. 
used /mnt/home/ranawee1/01_A_annua_trichome/differential_expression/do_all_fast_QC_nontrimmed.py. 
This script requries the working directory and print the output to file name you like by doing **<- file name** 
```
import os, sys
path = sys.argv[1]
os.chdir(path)
for root, dirs, files in os.walk(path):
        for f in files:
                if f.endswith(".fastq"): 
                    print("fastqc -o /mnt/home/ranawee1/01_A_annua_trichome/differential_expression/fastQC_before_trimming/ -f fastq %s" %(f))
                    
```
