# Metagenomic Shotgun Pipeline
![metagenomics_wallp](https://github.com/user-attachments/assets/e5932edc-178d-4655-a24d-49a003e845b0)

This pipeline was constructed for a shotgun metagenome analysis from mollusks.

This was equally contributor by:

**Gutiérrez-Sarmiento Wilbert** [wilbertgtzs@gmail.com] [https://github.com/WilbertGtzS]

**Fosado-Mendoza Mayela** [maye.fosado@gmail.com] [https://github.com/Mayfm]

**Lozano-Flores Carlos** [lozancarlos@yahoo.com] []

**Varela-Echavarría Alfredo** [avarela@unam.mx] []

This workflow was completed in DNA computing cluster from Laboratorio Nacional de Visualización Científica Avanzada (LAVIS) https://lavis.unam.mx/ at Universidad Nacional Autónoma de México (UNAM), Juriquilla, Querétaro, México. 🇲🇽

This is an example of workflow considering one sample of a metagenome study.

# Bioinformatic tools
Below, there are the bioinformatic tools and it's respective used version:
- [`FastQC`](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) `v0.11.3`
- [`Trimmomatic`](http://www.usadellab.org/cms/?page=trimmomatic) `v0.39`
- [`Cutadapt`]() `v0.00`
- [`KneadData`](https://huttenhower.sph.harvard.edu/kneaddata/) `v0.12.3`

  _Require_:  
  - Trimmomatic `v0.33`  
  - Bowtie `v2.5.4`  
  - TRF `v4.09.1` 
- [`Kraken2`](https://ccb.jhu.edu/software/kraken2/) `v2.0.8-beta`
- [`SPAdes`](https://cab.spbu.ru/software/spades/) `v4.2.0`
- [`Prodigal`](https://github.com/hyattpd/Prodigal) `v2.6.3`
- [`eggNOG`](http://eggnog-mapper.embl.de/) `v2.1.13`

This workflow is designed to perform the analyses in separate folders. Therefore, we create a folder for each step, and each script is intended to be executed within its corresponding directory. Additionally, this analysis was developed within an institutional cluster, so each tool is called using "module load". 

<pre lang="bash">
mkdir 0raw
mkdir 1fastqc
mkdir 2trimmomatic
mkdir 3cutadapt
mkdir 4kneaddata
mkdir 5kraken2
mkdir 6spades
mkdir 7prodigal
mkdir 8eggnog  
</pre>

# 0Raw_seq
Suppose you have Illumina paired-end sequencing reads saved in 0raw directory. Below is an example showing the correct structure of four lines from raw reads. The first line contains the sequence identifier and sequencing metadata. The second line shows the actual nucleotide sequence. The third line includes a plus sign (+), which separates the nucleotide sequence from the quality information. The fourth line contains the quality scores for each nucleotide. In this last line, the letters and symbols represent ASCII-encoded quality scores. You can check the quality value of each nucleotide here: https://www.drive5.com/usearch/manual/quality_score.html

Dlaeve1_R1.fastq

```
@NB501300:44:HMTKLBGXJ:1:11101:1284:1049 1:N:0:CTCGCTTCGG+TTGACTAGTA
ACCTTGACCCGTATGATAAAACAATCTTCAGCATNGTNNNNNCCAACAGCCATCTCTTNGACATCCATCGNTANGNTNGNCGGCTGATTGTCCTCACCAGTCTCGACCCTGGTCAATACNNNGTTAACGTCAGTGTCTCTGACGGAAAG
+
AAAAAEEAEEEEEEEEEEEEEEEEEEEEEEEEEE#EE#####AEEE/EEEEAEEEEEE#/EEEEEAEEEA#EE#/#E#/#EE/EEEEEEEEEAEEEE<EEEEEE<EE<EEEEE<EEEE/###/EEEE<AAEEEEEEEEEE<<EEE<EEE
```


Dlaeve1_R2.fastq
```
@NB501300:44:HMTKLBGXJ:1:11101:1284:1049 2:N:0:CTCGCTTCGG+TTGACTAGTA
TCTCAACGTCGTTGANNNGGACGTCAAGCTCCTGCTGCAGCACTCTCTGGAAATCTGACTTGAAGTTGGTGTAGAACTGCTCTTCAACCATATTCTCAAACCGGATGGTCAGCGCATTGTCCAGCATCTCTTGGGTTGTGCAGACAACG
+
AAAAAEEAEEEEEEE###EEE<EEEAE/EEEEEA<<EE/AE/EE<EEEEEAAEEEE/EAEEEEEEAEEEEEEEEE/AE<EEEEEEEEEEEEAEEEEA<E/AA/<EEAEEE/AAA//EEEE/EE<EEE/EEEEE/EEEEEEEEEAAEAEA
```

# 1FastQC
Once the raw sequence files are stored in the 0raw directory, the first quality check of the sequences is performed. Therefore, we enter the 1fastqc directory and use the following script.

<pre lang="bash"> module load fastqc/0.11.3 </pre>
<pre lang="bash"> fastqc ../0Raw_seq/Dlaeve1_R1.fastq -o . -t 4
 fastqc ../0Raw_seq/Dlaeve1_R2.fastq -o . -t 4 </pre>

The result will be an HTML file to visualize the quality graphs.

# 2Trimmomatic
The next step is the first quality filtering using Trimmomatic, based on the results observed in the initial FastQC report and according to the necessary filtering criteria. Therefore, we will run the following script inside the 2trimmomatic folder. It is important to note that the 2trimmomatic directory must also contain the Next_Illumina.fa file, which includes the Illumina adapter sequences, depending on the case. Here is an example of universal barcodes.

Next_Illumina.fa
```
>PrefixPE/1
CTGTCTCTTATACACATCT
>PrefixPE/2
CTGTCTCTTATACACATCT
```
Once we have the .fa file with the adapter sequences we want to remove, we apply the following script with the desired arguments or parameters.

<pre lang="bash"> module load trimmomatic/0.39 </pre>
<pre lang="bash"> trimmomatic PE -threads 4 \
   ../0Raw_seq/Dlaeve1_R1.fastq \
   ../0Raw_seq/Dlaeve1_R2.fastq \
   trimmed_Dlaeve1_R1.paired.fq.gz \
   trimmed_Dlaeve1_R1.unpaired.fq.gz \
   trimmed_Dlaeve1_R2.paired.fq.gz \
   trimmed_Dlaeve1_R2.unpaired.fq.gz \
   ILLUMINACLIP:Next_Illumina.fa:2:28:10 LEADING:28 TRAILING:28 SLIDINGWINDOW:4:28 MINLEN:50 </pre>

# 3Cutadapt
After trimming with Trimmomatic, it is necessary to run FastQC again to check the quality of the output sequences from Trimmomatic. In this case, we are interested in the *.paired.fq.gz files. Therefore, after reviewing the HTML reports, we decide to remove the first 9 nucleotides at the beginning of each sequence. Once inside the 3cutadapt directory, we use the following script. Note that in this case, the Cutadapt tool is installed within an Anaconda environment, so:

<pre lang="bash"> 
module load anaconda3/2025.06
source activate cutadapt-5.1 
</pre>

<pre lang="bash"> 
cutadapt -u 9 -o Dlaeve_R1_cut.fastq ../2trimmomatic/trimmed_Dlaeve_R1.paired.fq.gz 
cutadapt -u 9 -o Dlaeve_R2_cut.fastq ../2trimmomatic/trimmed_Dlaeve_R2.paired.fq.gz 
</pre> 

In this case, the output files are the *_cut.fastq files, which will be used in the next step.

# 4KneadData
The next step is to remove contaminant human sequences using the KneadData tool. First, it is necessary to download the human genome database using the following script within the 4kneaddata directory.

<pre lang="bash"> 
module load anaconda3/2025.06
source activate kneaddata-0.12.3
module load trimmomatic/0.33
module load bowtie/2.5.4
module load trf/4.09.1 
</pre>

<pre lang="bash"> 
kneaddata_database --download human_genome bowtie2 ./human_genome
</pre>

Once the database has been downloaded, we use the following script to run the sequence filtering in order to remove contamination from human sequences.

<pre lang="bash"> kneaddata \
  -i1 ../3cutadapt/Dlaeve_R1_cut.fastq \
  -i2 ../3cutadapt/Dlaeve_R2_cut.fastq \
  -o ./Dlaeve1_output \
  -db ./human_genome \
  -t 4 \
  --output-prefix Dlaeve1 \
  --remove-intermediate-output \
  --log ./Dlaeve1_output/Dlaeve_kneaddata.log
</pre>

In this step, the tool generates a separate directory for each paired-end sample. Inside each directory, we will find the filtered sequences: *_paired_R1.fastq and *_paired_R2.fastq.

# 5Kraken2
In the next step, we use Kraken2 for taxonomic assignment. First, it is necessary to download the Kraken2 database and configure it for use. In our case, this was done using the following script, which downloads the standard database along with additional libraries, and builds a custom library suitable for taxonomic classification.

<pre lang="bash"> module load kraken/2.0.8-beta </pre>

<pre lang="bash">
kraken2-build --standard --db kraken2_db --threads 32
kraken2-build --download-library fungi --threads 32 --db kraken2_db
kraken2-build --download-library plant --threads 32 --db kraken2_db
kraken2-build --download-library protozoa --threads 32 --db kraken2_db
kraken2-build --build --threads 32 --db kraken2_db
</pre>

Once the database has been created, we use the following script to perform taxonomic assignment with Kraken2.

<pre lang="bash">
kraken2 --db kraken2_db --threads 32 \
--paired ../4kneaddata/Dlaeve1/*_R1.fastq ../4kneaddata/Dlaeve1/*_R2.fastq \
--output Dlaeve1.kraken \
--report Dlaeve1.report 
</pre>

The output file with the .report extension contains the taxonomic classification. This file is then used in RStudio or Pavian metagenomic data analysis. The *.report file can also be converted into a *.biom format file.

<pre lang="bash">
kraken-biom/1.0.1
</pre>

<pre lang="bash">
kraken-biom \
  --report-files Dlaeve1.report \
  --output Dlaeve1.biom
</pre>

Now, the .biom file containing the taxonomic assignment information is useful for statistical analysis of microbiome composition in RStudio.

# 6SPAdes
In the next step, the sequences obtained from KneadData were used to assemble metagenomes from short reads. This process was carried out using MetaSPAdes with the following script.

<pre lang="bash"> module load spades/4.2.0 </pre>
<pre lang="bash"> 
  metaspades.py --only-assembler -1 ../4kneaddata/Dlaeve1/*_R1.fastq -2 ../4kneaddata/Dlaeve1/*_R2.fastq \
                -o Dlaeve1 -t 16 -m 200
  metaviralspades.py --only-assembler -1 ../4kneaddata/Dlaeve1/*_R1.fastq -2 ../4kneaddata/Dlaeve1/*_R2.fastq \
                     -o Dlaeve1 -t 16 -m 200 </pre>

# 7Prodigal
In the next step, the Prodigal tool was used to predict coding regions and obtain the open reading frames (ORFs). This was done using the following script.

<pre lang="bash"> module load prodigal/2.6.3 </pre>
<pre lang="bash"> prodigal -i ../6metaspades/Dlaeve1/contigs.fasta \
              -a proteins_Dlaeve1.faa -d genes_Dlaeve1.fna -f gff -o prodigal_Dlaeve1.gff -p meta </pre>
# 8eggNOG
The next step consists of functional annotation using eggNOG-mapper. First, it is important to have the database downloaded, so it is done as follows:

<pre lang="bash"> 
module load anaconda3/2025.06
source activate eggnog-mapper-2.1.13 </pre>

<pre lang="bash"> download_eggnog_data.py -F -P -M -H -d HMMER_DBS --dbname eggnog_db -y </pre>

Once the database has been downloaded, the functional analysis is carried out using the following script.

<pre lang="bash"> emapper.py -i ../7prodigal/proteins_Dlaeve1.faa --data_dir ./eggnog_db --cpu 16 --output sp_mollusk </pre>

Finally, the annotation file is generated in this format, and from this file, appropriate graphs are created according to the interests of each study.

| query                                 | seed_ortholog     | evalue   | score | eggNOG_OGs                                                                                   | max_annot_lvl            | COG_category | Description               | Preferred_name | GOs | EC | KEGG_ko | KEGG_Pathway | KEGG_Module | KEGG_Reaction | KEGG_rclass | BRITE | KEGG_TC | CAZy | BiGG_Reaction | PFAMs   |
|--------------------------------------|-------------------|----------|-------|----------------------------------------------------------------------------------------------|---------------------------|---------------|---------------------------|----------------|-----|----|----------|---------------|--------------|----------------|--------------|--------|----------|------|----------------|---------|
| NODE_1_length_158342_cov_25.987794_1 | 1005048.CFU_1160  | 3.52e-21 | 103.0 | COG0705@1\|root,COG0705@2\|Bacteria,1MYFP@1224\|Proteobacteria,2VPBJ@28216\|Betaproteobacteria | 28216\|Betaproteobacteria | S             | Rhomboid family protein   | Rhomboid       | -   | -  | -        | -             | -            | -              | -            | -      | -        | -    | -              | Rhomboid |


# Summary files
📁 Mollusk_microbiome
│  
├── 📁 0raw
│   ├── 📄 archivo1_1.txt  
│   └── 📄 archivo1_2.txt  
│  
├── 📁 1fastqc
│   ├── 📄 archivo2_1.txt  
│   └── 📄 archivo2_2.txt  
│  
├── 📁 2trimmomatic
│   ├── 📄 archivo3_1.txt  
│   └── 📄 archivo3_2.txt  
│  
├── 📁 3cutadapt
│   ├── 📄 archivo4_1.txt  
│   └── 📄 archivo4_2.txt  
│  
├── 📁 4kneaddata 
│   ├── 📄 archivo5_1.txt  
│   └── 📄 archivo5_2.txt  
│  
├── 📁 5kraken2
│   ├── 📄 archivo6_1.txt  
│   └── 📄 archivo6_2.txt  
│  
├── 📁 6spades
│   ├── 📄 archivo7_1.txt  
│   └── 📄 archivo7_2.txt  
│  
├── 📁 7prodigal  
│   ├── 📄 archivo8_1.txt  
│   └── 📄 archivo8_2.txt  
│  
├── 📁 8eggnog  
│   ├── 📄 archivo8_1.txt  
│   └── 📄 archivo8_2.txt  
│  
└── 📄 README.md
