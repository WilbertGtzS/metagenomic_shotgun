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


# 0Raw_seq
Suppose you have Illumina paired-end sequencing reads. Below is an example showing the correct structure of four lines from raw reads. The first line contains the sequence identifier and sequencing metadata. The second line shows the actual nucleotide sequence. The third line includes a plus sign (+), which separates the nucleotide sequence from the quality information. The fourth line contains the quality scores for each nucleotide. In this last line, the letters and symbols represent ASCII-encoded quality scores. You can check the quality value of each nucleotide here:: https://www.drive5.com/usearch/manual/quality_score.html

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
Considerar that...

<pre lang="bash"> module load fastqc/0.11.3 </pre>
<pre lang="bash"> fastqc ../0Raw_seq/*.fastq -o . -t 4 </pre>

# 2Trimmomatic

<pre lang="bash"> module load fastp/0.20.0 </pre>
<pre lang="bash"> module load trimmomatic/0.39 </pre>

<pre lang="bash"> trimmomatic PE -threads 2 \
   ../2rcorrector/unfixrm_C1_S36_R1_001.cor.fq \
   ../2rcorrector/unfixrm_C1_S36_R2_001.cor.fq \
   trimmed_C1_R1.paired.fq.gz \
   trimmed_C1_R1.unpaired.fq.gz \
   trimmed_C1_R2.paired.fq.gz \
   trimmed_C1_R2.unpaired.fq.gz \
   ILLUMINACLIP:TruSeq3_Illumina.fa:2:28:10 LEADING:28 TRAILING:28 SLIDINGWINDOW:4:28 MINLEN:50 </pre>

TruSeq3_Illumina.fa
>PrefixPE/1
TACACTCTTTCCCTACACGACGCTCTTCCGATCT
>PrefixPE/2
GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT

# 3Cutadapt

<pre lang="bash"> module load anaconda3/2025.06 </pre>
<pre lang="bash"> source activate cutadapt-5.1 </pre>

<pre lang="bash"> cutadapt -u 9 -o C1_R1_cut.fastq ../3trimmomatic/trimmed_C1_R1.paired.fq.gz </pre>
<pre lang="bash"> cutadapt -u 9 -o C1_R2_cut.fastq ../3trimmomatic/trimmed_C1_R2.paired.fq.gz </pre> 

# 4KneadData
<pre lang="bash"> module load anaconda3/2025.06 </pre>
<pre lang="bash"> source activate kneaddata-0.12.3 </pre>
<pre lang="bash"> module load trimmomatic/0.33 </pre>
<pre lang="bash"> module load bowtie/2.5.4 </pre>
<pre lang="bash"> module load trf/4.09.1 </pre>

<pre lang="bash"> kneaddata \
          -i1 "$R1" \
          -i2 "$R2" \
          -o "$SAMPLE_OUT" \
          -db "$DB_PATH" \
          -t "$THREADS" \
          --output-prefix "$BASE" \
          --remove-intermediate-output \
          --log "$SAMPLE_OUT/${BASE}_kneaddata.log" \
          --trf /mnt/data/alfredvar/wgutierrez/trf_link </pre>


# 5Kraken2

<pre lang="bash"> module load kraken/2.0.8-beta </pre>

<pre lang="bash"> kraken2 --db 1database_protzfungplant --threads 20 \
  --paired Seq_R1_cut.fastq /Seq_R2_cut.fastq \
 --output Seq/tax/Mollusk.kraken \
 --report Seq/tax/Mollusk.report </pre>

# 6SPAdes

<pre lang="bash"> module load spades/4.2.0 </pre>
<pre lang="bash"> metaspades.py --only-assembler -1 SRR15909356_paired_1.fastq -2 SRR15909356_paired_2.fastq -o SRR15909356 -t 16 -m 200 </pre>
<pre lang="bash"> metaviralspades.py --only-assembler -1 BC59_S46_paired_1.fastq -2 BC59_S46_paired_2.fastq -o BC59_viral -t 16 -m 200 </pre>

# 7Prodigal
<pre lang="bash"> module load prodigal/2.6.3 </pre>
<pre lang="bash"> prodigal -i /mnt/data/alfredvar/wgutierrez/5metaspades/BC59/contigs.fasta \
              -a proteins_BC59.faa -d genes_BC59.fna -f gff -o prodigal_BC59.gff -p meta </pre>
# 8eggNOG
<pre lang="bash"> module load anaconda3/2025.06 </pre>
<pre lang="bash"> source activate eggnog-mapper-2.1.13 </pre>
<pre lang="bash"> emapper.py -i ../7prodigal/proteins*.faa --data_dir /route/to/database/eggnog_db_v2.1.9 --cpu 16 --output sp_mollusk </pre>


| query                                 | seed_ortholog     | evalue   | score | eggNOG_OGs                                                                                   | max_annot_lvl            | COG_category | Description               | Preferred_name | GOs | EC | KEGG_ko | KEGG_Pathway | KEGG_Module | KEGG_Reaction | KEGG_rclass | BRITE | KEGG_TC | CAZy | BiGG_Reaction | PFAMs   |
|--------------------------------------|-------------------|----------|-------|----------------------------------------------------------------------------------------------|---------------------------|---------------|---------------------------|----------------|-----|----|----------|---------------|--------------|----------------|--------------|--------|----------|------|----------------|---------|
| NODE_1_length_158342_cov_25.987794_1 | 1005048.CFU_1160  | 3.52e-21 | 103.0 | COG0705@1\|root,COG0705@2\|Bacteria,1MYFP@1224\|Proteobacteria,2VPBJ@28216\|Betaproteobacteria | 28216\|Betaproteobacteria | S             | Rhomboid family protein   | Rhomboid       | -   | -  | -        | -             | -            | -              | -            | -      | -        | -    | -              | Rhomboid |
