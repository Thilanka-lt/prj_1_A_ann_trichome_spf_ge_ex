# **Trichome Specific Gene Expression**

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

## 2. Gene family identification  
### 2.1 Prepairing and downloading data. 


Unzip the protien sequnce datafile.  
	gnzip GCA_003112345.1_ASM311234v1_protein.faa. 
  
### 2.2 Blasting all protien sequences agains each other.  
This is done to identify the sequnce similarity between each other.  
__pipeline__ 

* load modules. 
	module load BLAST

* Build the database of protein sequences.  
		
	formatdb -i #NAME_OF_YOUR_FILE# -p T

	-I - input file name. 
	-p -indicate type of file(protien) then put T. So the final extention should be - p T

* Run BLAST
	blastall -p blastp -d Athaliana_167_TAIR10.protein_primaryTranscriptOnly.fa.mod.fa -i Ath_genes.fas -o ath_to_ath.blastp -m 8 -e 0.00001
	
	-p - program name. In this case we are using protein blast. So it will be blastp.
	-d - database
	-m what data you want to see
	-e the E threshold 

### 2.3 MCL (Markov Cluster Algorithm) analysis on A. anuua protien sequnces. 
The main focus here is to identify the gene families. MCL provides the ability to construct paralogous groups using protien sequnces.  

__pipeline__  
  
* copy the protien sequnce file (GCA_003112345.1_ASM311234v1_protein.faa) into */mnt/home/ranawee1/prj_1_A_ann_trichome_spf_ge_ex/A_annua_data/protien_database*
* In HPCC need to load modules that needed to work on orthoMCL pipeline.  

  	module purge   
	module load icc/2016.3.210-GCC-5.4.0-2.26 impi/5.1.3.181  
	module load OrthoMCL/2.0.9-custom-Perl-5.24.0  
  	module load BLAST/2.2.26-Linux_x86_64 
 
  

	
* First need to convert the columnar BLAST file format (What we get from the blastal) to .abc file format that can be used in MCL.
	-cut will identify the e-value column created during the -m 8 of BLAST 
	
	cut -f 1,2,11 seq.cblast > seq.abc
	
* The newly created seq.abc file is loaded by mcxload, which writes both a network file seq.mci and a dictionary file seq.tab.
	The --stream-mirror option ensures that the resulting network will be undirected. 
	The --abc-neg-log10 transforms the numerical values in the input (the BLAST E-values) by taking the logarithm in base 10 and subsequently negating the sign.
	ceil(200) transformed values are capped so that any E-value below 1e-200 is set to a maximum allowed edge weight of 200.
	
	mcxload -abc seq.abc --stream-mirror --stream-neg-log10 -stream-tf 'ceil(200)' -o seq.mci -write-tab seq.tab
	
* Next we can creat a abstract lustering representation by running MCL.  We use seq.mci output file to make clusters at different  inflation values (higher inflation value means finer clustering). Running this camman the will automatically create out.seq.mci.I14, 20, 40….150. 
	
	mcl seq.mci -I 1.4
	(I did clustering at 1.4,  2,  4, 6, 10, 12, 15) 
	
* Labled outputs can be created by using maxdump. This will create a dump.seq.mci.I14 --> 150 files. Also this will separate the clusters with lines and tab characters.
	
	mcxdump -icl out.seq.mci.I14 -tabr seq.tab -o dump.seq.mci.I14
	mcxdump -icl out.seq.mci.I20 -tabr seq.tab -o dump.seq.mci.I20
	mcxdump -icl out.seq.mci.I40 -tabr seq.tab -o dump.seq.mci.I40
	mcxdump -icl out.seq.mci.I60 -tabr seq.tab -o dump.seq.mci.I60
	
	Also wc -1 dump* can show the numer of cluters at each I values
	
*  Different clustering does not ensure strict clustering. So stricly nesting set of clustering can be created from following code
	This will generate P1 --> P7 which are strict syper clustring of all of the following.
	
	clm order -prefix P out.seq.mci.I{14,20,60}
	
	You can make labled clusters using 
	
	mcxdump -icl P7  -o P1_150_lbld  -tabr seq.tab
	
* It is possible to encode the nested clusterings as a tree, in this case of depth just 7.
	It is transformed to Newick format by mcxdump. The branch lengths simply indicate the level at which nodes and clusters join, and should not be given any further meaning. 
	
	clm order -o seq.mcltree out.seq.mci.I{14,20,60}
	mcxdump -imx-tree seq.mcltree -tab seq.tab --newick -o tree.nwk 
	
* Small clusters are to some extent explained by the input graph already consisting of different components. Therefore it is useful to obtain the baseline coarse clustering consisting of the natural maximum components of the input graph (also known as connected components). The program clm close does just this. 
	
	Produce the seq.base output
	
	clm close -imx seq.mci --write-cc -o seq.base
	
* The granularity of each cluster can be visualized from.
	
	MCL_analysis]$ clxdo granularity seq.base out.seq.mci.I14
	
	Or you can do it for all the clusters
	
	MCL_analysis]$ clxdo granularity seq.base out.seq.mci.I{14,20,40…….150}
	
	
	You can also specify a minimum cluster size
	
	clxdo granularity_gq 5 seq.base out.seq.mci.I14
	
	You can specify all the outputs for all the mcs. Files
	
	clxdo granularity_gq 5 seq.base out.seq.mci.I{14,20,40………….150}
	
	
* It is very useful to assess how close two clusterings are. To this end, issue the following. 
	
	clm dist --chain out.seq.mci.I{14,20,60} 
	
	The --chain option causes only consecutive clustering to be compared, whereas the default behavior is to do all pairwise comparisons. 
	
	d=13802 d1=12578 d2=1224 nn=65553 c1=6249 c2=7907 v=1130 n1=out.seq.mci.I14 n2=out.seq.mci.I20 vol=[1130,1914,3176,5831,17240]
	
	d = distance between clusters

### 2.4 Run HMMER on A_annua protien sequnces againts all pfam domains







