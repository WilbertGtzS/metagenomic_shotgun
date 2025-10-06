# Metagenomic Shotgun Pipeline
![metagenomics_wallp](https://github.com/user-attachments/assets/e5932edc-178d-4655-a24d-49a003e845b0)

This pipeline was constructed for de novo assembly of transcriptome research for a non-model organism.

This was equally contributor by:

**Gutiérrez-Sarmiento Wilbert** [wilbertgtzs@gmail.com] [https://github.com/WilbertGtzS]

**Fosado-Mendoza Mayela** [maye.fosado@gmail.com] [https://github.com/Mayfm]

**Lozano-Flores Carlos** [lozancarlos@yahoo.com] []

**Alfredo Varela-Echavarría** [avarela@unam.mx] []

This workflow was completed in DNA computing cluster from Laboratorio Nacional de Visualización Científica Avanzada (LAVIS) https://lavis.unam.mx/ at Universidad Nacional Autónoma de México (UNAM), Juriquilla, Querétaro, México. 🇲🇽

This is an example of workflow considering the next number of samples of an experimental transcriptome.

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



# FastQC
module load fastqc/0.11.3
mkdir -p ../results/fastqc
fastqc -o ../results/fastqc -t 4 ../data/raw/*_R1*.fastq ../data/raw/*_R2*.fastq

<pre lang="bash"> fastqc -o ../results/fastqc -t 4 ../data/raw/*_R1*.fastq ../data/raw/*_R2*.fastq </pre>

# Trimmomatic
# Cutadapt
# KneadData
# Kraken2
# SPAdes
# Prodigal
# eggNOG
