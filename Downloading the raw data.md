## 1. Download the raw data
The expression data are dowloaded from the NCBI SRA database.

Species | Tissue | Accession
-------- |-------|---------
*Artemisia annua*| Trichome |SRX3562825  
*Artemisia annua*| Flower |SRX3562826 
*Artemisia annua*| Seed |SRX3562827 
*Artemisia annua*| Bud |SRX3562828 
*Artemisia annua*| Epidermis |SRX3562829 
*Artemisia annua*| Root |SRX3562830
*Artemisia annua*| Stem |SRX3562831
*Artemisia annua*| Old leaf |SRX3562832
*Artemisia annua*| Young leaf |SRX3562833 

 * Use the **qsub_slurm.py** to submit this as job.
 	fastq-dump SRX3562827 ---> all the accesions in a get_data.sh
 * Also can use the script below and submit it through the qsub_slurm.py
 ~~~
 # this code uses two arguments. The first on is to acess the file with all the SRR accession and the secon one is the name of your output file.

 /mnt/home/ranawee1/01_A_annua_trichome/differential_expression/generate_dump_seq.py

 #generate_dump_seq.py code

 import sys
 data_file = open(sys.argv[1], "r").readlines()
 write_file = open(sys.argv[2], "w")
 for i in data_file:
     write_file.write("fastq-dump --split-3 %s" %(i))
 write_file.close

 # when submitting the looks like we have to load noyt only SRA-Toolkit but also Perl/5.28.1.

 python qsub_slurm.py -f submit -c full_dump_file.txt -p 4 -u ranawee1 -w 1200  -m 10 -mo 'SRA-Toolkit Perl/5.28.1' -wd ./
 ~~~

 * Download **1- genome dataset**,  **2- Protien datasets** and **3- Annotatin** from NCBI genome (They have the recently published genomes in 2018). 
 ```
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/003/112/345/GCA_003112345.1_ASM311234v1/GCA_003112345.1_ASM311234v1_genomic.fna.gz
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/003/112/345/GCA_003112345.1_ASM311234v1/GCA_003112345.1_ASM311234v1_protein.faa.gz
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/003/112/345/GCA_003112345.1_ASM311234v1/GCA_003112345.1_ASM311234v1_genomic.gff.gz
```
