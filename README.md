# Metagenomic Shotgun Pipeline
![metagenomics_wallp](https://github.com/user-attachments/assets/e5932edc-178d-4655-a24d-49a003e845b0)

This pipeline was constructed for de novo assembly of transcriptome research for a non-model organism.

This was equally contributor by:

**Gutiérrez-Sarmiento Wilbert** [wilbertgtzs@gmail.com] [https://github.com/WilbertGtzS]

**Fosado-Mendoza Mayela** [maye.fosado@gmail.com] [https://github.com/Mayfm]

**Lozano-Flores Carlos** [lozancarlos@yahoo.com] []

**Alfredo Varela-Echavarría** [avarela@unam.mx] []

This workflow was completed in DNA computing cluster from Laboratorio Nacional de Visualización Científica Avanzada (LAVIS) https://lavis.unam.mx/ at Universidad Nacional Autónoma de México (UNAM), Juriquilla, Querétaro, México. 🇲🇽

This is an example of workflow considering the next number of samples of an experimental metagenome

# Bioinformatic tools
Below, there are the bioinformatic tools and it's respective used version:
- [`FastQC`](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) `v0.11.3`
- [`Trimmomatic`](http://www.usadellab.org/cms/?page=trimmomatic) `v0.39`
- [`Cutadapt`]() `v0.00`
- [`KneadData`](https://huttenhower.sph.harvard.edu/kneaddata/) `v0.12.3`
  _Requiere_:  
  - Trimmomatic `v0.33`  
  - Bowtie `v2.5.4`  
  - TRF `v4.09.1` 
- [`Kraken2`](https://ccb.jhu.edu/software/kraken2/) `v2.0.8-beta`
- [`SPAdes`](https://cab.spbu.ru/software/spades/) `v4.2.0`
- [`Prodigal`](https://github.com/hyattpd/Prodigal) `v2.6.3`
- [`eggNOG`](http://eggnog-mapper.embl.de/) `v2.1.13`



# 1FastQC
module load fastqc/0.11.3
mkdir -p ../results/fastqc
fastqc -o ../results/fastqc -t 4 ../data/raw/*_R1*.fastq ../data/raw/*_R2*.fastq
<pre lang="bash"> module load fastqc/0.11.3 </pre>
<pre lang="bash"> fastqc -o ../results/fastqc -t 4 ../data/raw/*_R1*.fastq ../data/raw/*_R2*.fastq </pre>

# 2Trimmomatic
#trimmomatic PE ../1raw/"${base}"*_R1.fastq /1raw/"${base}"*_R2.fastq /1raw/"${base}"_R1.trimmed_PE.fastq /1raw/"${base}"_R1.trimmed_SE.fastq /1raw/"${base}"_R2.trimmed_PE.fastq /1raw/"${base}"_R2.trimmed_SE.fastq SLIDINGWINDOW:4:28 MINLEN:50



# 3Cutadapt
# 4KneadData
# 5Kraken2
# 6SPAdes
# 7Prodigal
# 8eggNOG

<pre lang="bash"> module load anaconda3/2025.06 </pre>
<pre lang="bash">source activate eggnog-mapper-2.1.13 </pre>
<pre lang="bash"> emapper.py -i ../7prodigal/proteins*.faa --data_dir /route/to/database/eggnog_db_v2.1.9 --cpu 16 --output sp_mollusk </pre>


