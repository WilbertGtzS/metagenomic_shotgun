# Metagenomic_Shotgun
![metagenomics_wallp](https://github.com/user-attachments/assets/e5932edc-178d-4655-a24d-49a003e845b0)

This pipeline was constructed for de novo assembly of transcriptome research for a non-model organism.

This was equally contributor by:

Gutiérrez-Sarmiento Wilbert [wilbertgtzs@gmail.com] [https://github.com/WilbertGtzS]

Fosado-Mendoza Mayela [maye.fosado@gmail.com] [https://github.com/Mayfm]

Alfredo Varela-Echavarría [avarela@unam.mx] []

This workflow was completed in DNA computing cluster from Laboratorio Nacional de Visualización Científica Avanzada (LAVIS) https://lavis.unam.mx/ at Universidad Nacional Autónoma de México (UNAM), Juriquilla, Querétaro, México. 🇲🇽

This is an example of workflow considering the next number of samples of an experimental transcriptome.

# Bioinformatic tools
fastqc/0.11.3
trimmomatic/0.39
kraken/2.0.8-beta
kneaddata-0.12.3 (require trimmomatic/0.33, bowtie/2.5.4, trf/4.09.1)
spades/4.2.0
prodigal/2.6.3
eggnog-mapper-2.1.13 


# FastQC script
module load fastqc/0.11.3
mkdir -p ../results/fastqc
fastqc -o ../results/fastqc -t 4 ../data/raw/*_R1*.fastq ../data/raw/*_R2*.fastq
