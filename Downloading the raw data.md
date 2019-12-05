
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

* Download **1- genome dataset**,  **2- Protien datasets** and **3- Annotatin** from NCBI genome (They have the recently published genomes in 2018). 
```
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/003/112/345/GCA_003112345.1_ASM311234v1/GCA_003112345.1_ASM311234v1_genomic.fna.gz
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/003/112/345/GCA_003112345.1_ASM311234v1/GCA_003112345.1_ASM311234v1_protein.faa.gz
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/003/112/345/GCA_003112345.1_ASM311234v1/GCA_003112345.1_ASM311234v1_genomic.gff.gz
```







