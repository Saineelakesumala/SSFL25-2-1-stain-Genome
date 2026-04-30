
# SSFL25-2-1
Genome assembly of Pyricularia oryzae  stain SSFL25-2-1
# MyGenome - SSFL25-2-1
**Repository URL:** https://github.com/Sai-Neela-Kesumala/SSFL25-2-1

This repository consists bash piplines for genome sequencing cleanup, assembly, and annotation (from raw reads to an assembly, BLAST-queried genome).

---

## Table of Contents
1. [Download Datasets from the Farman Lab Mac](#Download-Datasets-from-the-Farman-Lab-Mac)
2. [Assess Sequence Quality with FASTQC](#Assess-Sequence-Quality-with-FASTQC)
3. [Trim Adaptors and Poor Quality Sequence with Trimmomatic](#Trim-Adaptors-and-Poor-Quality-Sequence-with-Trimmomatic)
4. [Generate an Optimized MyGenome Assembly using Velvet and SPAdes](#Generate-an-Optimized-MyGenome-Assembly-using-Velvet-and-SPAdes)
5. [Perform Genome Post Processing for NCBI Submission](#Perform-Genome-Post-Processing-for-NCBI-Submission)
6. [Assess Genome Quality using BUSCO](#Assess-Genome-Quality-using-BUSCO)
7. [Genome Interrogation using BLAST](#Genome-Interrogation-using-BLAST)
8. [Perform Gene Predictions](#Perform-Gene-Predictions)
9. [Visualize Genes in Genome Browser](#Visualize-Genes-in-Genome-Browser)
---
 
## Download Datasets from the Farman Lab Mac

1. Connect to the remote server and copy raw sequencing reads to your working directory:

```
 scp -r ngs@10.163.188.11:Desktop/PR0069 ske300/unix/sequence/SSFL25-2-1
```
---

## Assess Sequence Quality with FASTQC

1. Run FastQC on the raw reads:

```
 fastqc ske300/unix/sequence/SSFL25-2-1/SSFL25-2-1_1.fq.gz ske300/unix/sequence/SSFL25-2-1/SSFL25-2-1_2.fq.gz -o ~/sequence
```

2. View the r1 raw read report by transfering from VM to local remote served and open the generated HTML file in a browser:

```
scp -r ske300@ske300.cs.uky.edu:~/sequences/SSFL25-2-1/SSFL25-2-1_1_fastqc.html .
```

3.View the r2 raw read report by transfering from VM to local remote served and open the generated HTML file in a browser:

```
scp -r ske300@ske300.cs.uky.edu:~/sequences/SSFL25-2-1/SSFL25-2-1_2_fastqc.html .
```

#### PR0069.1FastQC Summary (Before Trimming)

| Module | Status |
|---|---|
| Basic Statistics | GOOD |
| Per base sequence quality | GOOD |
| Per tile sequence quality | WARN |
| Per sequence quality scores | GOOD |
| Per base sequence content | WARNING |
| Per sequence GC content | WARNING |
| Per base N content | GOOD |
| Sequence Length Distribution | GOOD |
| Sequence Duplication Levels | GOOD |
| Overrepresented sequences | WARNING |
| Adapter Content | FAILED |

> **Warnings:** Per tile sequence quality, Per sequence GC content and Sequence Length Distribution — adapters must be removed for further assembly analysis. 

<details>
<summary> Assess Sequence Quality  </summary>  
 
![Summary](FASTQ/BASIC_STATISTICS_SsFL25_2_1_1.png)
![Per Base Sequence Quality](FASTQ/PER_BASE_SEQUENCE-QUALITY.png)
![Per Tile Sequence Quality](FASTQ/PER_TILE_SEQUENCE_QUALITY_SsFL25-2-1-1.png)
![Per sequence quality scores](FASTQ/PER_SEQUENCE-QUALITY_SCORES.png)
![Per base sequence content](FASTQ/PER_BASE_SEQUENCE-CONTENT-SsFL25_2_1-1.png)
![Per sequence GC content](FASTQ/PER_SEQUENCE_GC_CONTENT-SsFL25_2_1-1.png)
![Per base N content](FASTQ/PER_BASE_N-CONTENT-SsFL25_2_1-1.png)
![Sequence Length Distribution](FASTQ/SEQUENCE_LENGTH_DISTRIBUTION-SsFL25_2_1-1.png)
![Sequence Duplication Levels](FASTQ/SEQUENCE_DUPLICATION_LEVELS_SsFL25_2_1-1.png)
![Overrepresented sequences](FASTQ/OVERREPRESENTED_SEQUENCE_SsFL25_2_1.png)
![Adapter Content](FASTQ/ADAPTOR_CONTENT_SsFL25_2_1_1.png)




6. PR0069.2 FastQC Summary

| Module | Status |
|---|---|
| Basic Statistics | GOOD |
| Per base sequence quality | GOOD |
| Per tile sequence quality | WARNING |
| Per sequence quality scores | GOOD |
| Per base sequence content | WARNING |
| Per sequence GC content | WARNING |
| Per base N content | GOOD |
| Sequence Length Distribution | GOOD |
| Sequence Duplication Levels | GOOD |
| Overrepresented sequences | WARNING |
| Adapter Content | FAILED |

<details>
<summary> Assess Sequence Quality  </summary>  
 
![Summary](FASTQ2/BASIC_STATISTICS_SsFL25_2_1_2.png)
![Per Base Sequence Quality](FASTQ2/PER_BAE_SEQUENCE_QUALITY_SsFL25_2_1_2.png)
![Per Tile Sequence Quality](FASTQ2/PER_TILE_SEQUENCE_QUALITY_SsFL25_2_1_2.png)
![Per sequence quality scores](FASTQ2/PER_SEQUENCE_QUALITY_SCORES_SsFL25_2_1_2.png)
![Per base sequence content](FASTQ2/PER_BASE_SEQUENCE_CONTENT_SsFL25_2_1_2.png)
![Per sequence GC content](FASTQ2/PER_SEQUENCE_GC_CONTENT_SsFL25_2_1_2.png)
![Per base N content](FASTQ2/PER_BASE_N_CONTENT_SsFL25_2_1_2.png)
![Sequence Length Distribution](FASTQ2/SEQUENCE_LENGTH_DISTRIBUTION_SsFL25_2_1_2.png)
![Sequence Duplication Levels](FASTQ2/SEQUENCE_DUPLICATION_LEVELS_SsFL25_2_1_2.png)
![Overrepresented sequences](FASTQ2/OVERREPRESENTED_SEQUENCE_SsFL25_2_1_2.png)
![Adapter Content](FASTQ2/ADAPTER_CONTENT_SsFL25_2_1_2.png)



> **Warnings:** Per tile sequence quality, Per sequence GC content and Sequence Length Distribution — adapters must be removed for further assembly analysis. 

---
  
## Trim Adaptors and Poor Quality Sequence with Trimmomatic
1. Edit the list of the adaptor sequences file by adding 20 G to the file:
   
 ```
 nano ske300/unix/sequence/adaptors.fa
 ```

2. Run Trimmomatic to remove adapters and low-quality bases on both paired and unpaired raw reads:

```
 java -jar sequences/trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog Br80_errorlog.txt ske300/unix/sequence/SSFL25-2-1/SSFL25-2-1_1.fq.gz ske300/unix/sequence/SSFL25-2-1/SSFL25-2-1_2.fq.gz SSFL25-2-1_1_paired.fastq SSFL25-2-1_1_unpaired.fastq SSFL25-2-1_2_paired.fastq SSFL25-2-1_2_unpaired.fastq ILLUMINACLIP:ske300/unix/sequence/PR0069/adaptors.fa:2:30:10 SLIDINGWINDOW:20:20 MINLEN:125
```
3. Re-run FastQC on trimmed paired and unpaired reads to confirm improvement:

```
 fastqc  SSFL25-2-1_1_paired.fastq SSFL25-2-1_2_paired.fastq  SSFL25-2-1_1_unpaired.fastq SSFL25-2-1_2_unpaired.fastq
```

| Module | Status |
|---|---|
| Basic Statistics | PASS |
| Per base sequence quality | PASS |
| Per tile sequence quality | WARNING |
| Per sequence quality scores | PASS |
| Per base sequence content | WARNING |
| Per sequence GC content | WARNING |
| Per base N content | PASS |
| Sequence Length Distribution | WARNING |
| Sequence Duplication Levels | PASS |
| Overrepresented sequences | PASS |
| Adapter Content | PASS |

> **After trimming:** Adapter content warning was resolved. All critical modules now pass.

<details>
<summary> Assess Sequence Quality  </summary>  
 
4. SSFL25-2-1_1_paired.fastq FastQC Summary  
![Summary](FASTQU_PAIRED/Basic_stats_SsFL-25-1-1-PAIRED.png)
![Per Base Sequence Quality](FASTQU_PAIRED/PER_BASE_SEQUENCE_QUALITY_SsFL25-1-1-PAIRED.png)
![Per Tile Sequence Quality](FASTQU_PAIRED/Per_tile_sequence_quality-SsFL25-1-1-PAIRED.png)
![Per sequence quality scores](FASTQU_PAIRED/Per_sequence_quality_scores_SsFL25-1-1-paired.png)
![Per base sequence content](FASTQU_PAIRED/Per_base_sequence_content-SsFL25-1-1-PAIRED.png)
![Per sequence GC content](FASTQU_PAIRED/Per_sequence_GC_content-SsFL25-1-1PAIRED.png)
![Per base N content](FASTQU_PAIRED/Per_base_N_content_SsFL-1_1-PAIRED.png)
![Sequence Length Distribution](FASTQU_PAIRED/Sequence_Length_Distribution_SsFL25-1-1-PAIRED.png)
![Sequence Duplication Levels](FASTQ/SEQUENCE_DUPLICATION_LEVELS_SsFL25_2_1-1.png)
![Overrepresented sequences](FASTQ/OVERREPRESENTED_SEQUENCE_SsFL25_2_1.png)
![Adapter Content](FASTQU_PAIRED/Adapter_Content-SsFL25-1-1PAIRED.png)

| Module | Status |
|---|---|
| Basic Statistics | PASS |
| Per base sequence quality | PASS |
| Per tile sequence quality |  WARNING |
| Per sequence quality scores | PASS |
| Per base sequence content | WARNING |
| Per sequence GC content |  WARNING |
| Per base N content | PASS |
| Sequence Length Distribution | WARNING |
| Sequence Duplication Levels | PASS |
| Overrepresented sequences | PASS |
| Adapter Content | PASS |


<details>
<summary> Assess Sequence Quality  </summary>  
 
 6. SSFL25-2-1_1_unpaired.fastq FastQC Summary
![Summary](FASTQC_UNPAIRED/BAS_STAT.png)
![Per Base Sequence Quality](FASTQC_UNPAIRED/Per_base_sequence_quality.png)
![Per Tile Sequence Quality](FASTQC_UNPAIRED/Per_tile_sequence_quality.png)
![Per Sequence Quality Scores](FASTQC_UNPAIRED/Per_sequence_quality_scores.png)
![Per Base N Content](FASTQC_UNPAIRED/Per_base_N_content.png)
![Sequence Length Distribution](FASTQC_UNPAIRED/Sequence_Length_Distribution.png)
![Sequence Duplication Levels](FASTQC_UNPAIRED/Sequence_Duplication_Levels.png)
![Adapter Content](FASTQC_UNPAIRED/Adapter_Content.png)



> **After trimming:** Adapter content warning was resolved. All critical modules now pass.


| Module | Status |
|---|---|
|Basic Statistics| PASS |
| Per base sequence quality | PASS |
| Per tile sequence quality | WARNING |
| Per sequence quality scores | PASS |
| Per base sequence content | PASS |
| Per sequence GC content | WARN |
| Per base N content | PASS |
| Sequence Length Distribution | WARNING |
| Sequence Duplication Levels | PASS |
| Overrepresented sequences | PASS |
| Adapter Content | WARNING |

<details>
<summary> Assess Sequence Quality  </summary>  
 
6. SSFL25-2-1_2_paired.fastq FastQC Summary
![Summary](SSFL25-2-1-2-PAIRED/Basic_stats.png)
![Per Base Sequence Quality](SSFL25-2-1-2-PAIRED/Per_base_sequence_quality.png)
![Per Tile Sequence Quality](SSFL25-2-1-2-PAIRED/Per_tile_sequence_quality.png)
![Per Sequence Quality Scores](SSFL25-2-1-2-PAIRED/Per_sequence_quality_scores.png)
![Per Base Sequence Quality](SSFL25-2-1-2-PAIRED/Per_base_sequence_quality.png)
![Per Sequence GC Content](SSFL25-2-1-2-PAIRED/Per_sequence_GC_content.png)
![Sequence Length Distribution](SSFL25-2-1-2-PAIRED/Sequence_Length_Distribution.png)
![Sequence Duplication Levels](SSFL25-2-1-2-PAIRED/Sequence_Duplication_Levels.png)
![adapter_after.png](SSFL25-2-1-2-PAIRED/Adapter_Content.png)



> **After trimming:** Adapter content warning was resolved. All critical modules now pass.

| Module | Status |
|---|---|
| Basic Statistics | PASS |
| Per base sequence quality | PASS |
| Per tile sequence quality | WARNING |
| Per sequence quality scores | PASS |
| Per base sequence content | PASS |
| Per sequence GC content | WARN |
| Per base N content | PASS |
| Sequence Length Distribution | WARNING |
| Sequence Duplication Levels | PASS |
| Overrepresented sequences | PASS |
| Adapter Content | WARNING |

<details>
<summary> Assess Sequence Quality  </summary>  
 
7. FastQC Summary  of SSFL25-2-1_2_unpaired.fastq read (After Trimming)
![Summary](IMAGES/BASICS STATSTICS_PR0069_2_UNPAIRED.png)
![Per Base Sequence Quality](IMAGES/PER_BASE_SEQUENCE_CONTENT_PR0069_2_UNPAIRED.png)
![Per Tile Sequence Quality](IMAGES/PER_TILE_SEQUENCE_QUALITY_PR0069_2_UNPAIRED.png)
![Per Sequence Quality Scores](IMAGES/PER_SEQUENCE_QUALITY_SCORES_PR0069_2_UNPAIRED.png)
![Per Sequence GC Content](IMAGES/PER_SEQUENCE_GC_CONTENT_PR0069_2_UNPAIRED.png)
![Per Base N Content](IMAGES/PER_BASE_N_CONTENT_PR0069_2_UNPAIRED.png)
![Sequence Length Distribution](IMAGES/SEQUENCE_LENGTH_DISTRIBUTION_PR0069_2_UNPAIRED.png)
![Sequence Duplication Levels](IMAGES/SEQUENCE_DUPLICATION_LEVELS_PR0069_2_UNPAIRED.png)
![adapter_after.png](IMAGES/ADAPTER_CONTENT_PR0069_2_UNPAIRED.png)



> **After trimming:** Adapter content warning was resolved. All critical modules now pass.

---

### Optimized MyGenome Assembly using Velvet 

1. Transfer trimmed paired and unpaired reads to the MCC cluster:

```
 scp SSFL25-2-1_1_paired.fastq SSFL25-2-1_2_paired.fastq  SSFL25-2-1_1_unpaired.fastq SSFL25-2-1_2_unpaired.fastq ske300@mcc.uky.edu:/project/farman_s26abt480/ske300/
```

2. Run velvet for genome assembly using a range of K-mer values at 10fold with obtained reference from k-mer adviosory:

```
 sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/velvetoptimiser.sh /project/farman_s26abt480/ske300/SSFL25-2-1 13 43 10
```

```
 sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/velvetoptimiser.sh /project/farman_s26abt480/ske300/SSFL25-2-1 41 80 10
```

```
 sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/velvetoptimiser.sh /project/farman_s26abt480/ske300/SSFL25-2-1 23 53 10
```

```
sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/velvetoptimiser.sh /project/farman_s26abt480/ske300/SSFL25-2-1 51 80 10
```

3. Re-run velvet using a range of K-mer values at 2 fold:

```
sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/velvetoptimiser.sh /project/farman_s26abt480/ske300/SSFL25-2-1 40 120 2
```

```
sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/velvetoptimiser.sh /project/farman_s26abt480/ske300/SSFL25-2-1 51 80 2
```
---
 
### Optimized MyGenome Assembly using SPAdes


1. Submit SPAdes job to the cluster:

```
sbatch /project/farman_s26abt480/ske300/SSFL25-2-1/spades-paired.sh . /project/farman_s26abt480/ske300/SSFL25-2-1  SSFL25-2-1
```
 
| Metric | Velvet-step10 (k=31) | Velvet-step2 |SPAdes|
|---|---|---|---|
| Genome size| 43031414 | 43045664 | 45561195 | 44641790 |
| contig (bp) | 18522 | 18629 | 23561 | 22803 |
| N50 | 17569 | 17723 | 42969 | 39937 |

> **Optimal assembly:** SPAdes genome assembly analysis provided highest number of N50 contig. 
Hiher N50 contigs are expected to havr higher genome assmebly sequencing coverage:

- The highest N50 of 42969 bp
- Total length closest to the expected genome size

Velvet with ten and two fold provided lesser N50 contigs.

---
 
## Perform Genome Post Processing for NCBI Submission
1.changing scaffolds fasta heading:

 ```
 cp farman_s26abt480/SCRIPTs/SimpleFastaHeaders.pl /project/farman_s26abt480/ske300/SSFL25-2-1/SimpleFastaHeaders.pl
```

```
perl /project/farman_s26abt480/ske300/SSFL25-2-1/SimpleFastaHeaders.pl /project/farman_s26abt480/ske300/SSFL25-2-1/SSFL25-2-1_spades_pairedassembly/scaffolds.fasta  
```

```
 cp /project/farman_s26abt480/SLURM_SCRIPTs/GenomePostProcess.sh /project/farman_s26abt480/ske300/SSFL25-2-1/GenomePostProcess.sh
``` 

```
 sbatch BuscoSingularity.sh /project/farman_s26abt480/ske300/SSFL25-2-1/SSFL25-2-1_final.fasta
```
---

 
### Bandage Visualization of Optimal Assembly

**Bandage graph of optimal SPAdes assembly:**

![bandage_assembly.png](Bandage/SsFL25-1-1-spades-paired.png)

> The Bandage image shows the assembly graph optiained from the spades analysis. Most contigs form clean linear paths indicating minimal ambiguity. A small number of branching nodes correspond to repetitive genomic regions.

---
 
## Genome Interrogation using BLAST

1. Transferring query mitochondrial reference sequence from another directory:

```
 cp /project/farman_s26abt480/RESOURCES/MoMitochondrion.fasta /project/farman_s26abt480/ske300/SSFL25-2-1/MoMitochondrion.fasta
```

 
2. Blast the genome assembly against Mitochondrial subject

```
 singularity run --app blast2120 /share/singularity/images/ccs/conda/amd-conda1-centos8.sinf blastn -query MoMitochondrion.fasta -subject SSFL25-2-1_final.fasta -evalue 1e-50 -max_target_seqs 2000 -outfmt '6 qseqid sseqid slen length qstart qend sstart send btop' -out MoMitocondrion.PR0069.BLAST
```


3. Creating 90% BLAST hit sequence into a csv file :

```
awk '$4/$3 >= 0.9 {print $2 ",mitocondrion"}' MoMitocondrion.SSFL25-2-1.BLAST > SSFL25-2-1.csv
```

2. Mitochontrial hits with contigs copied into CSV:

```
 cp MoMitocondrion.PR0069.BLAST MyPR0069.csv
```

---

### BLAST Findings Summary

> **Mitochondrial contigs:** BLAST against reference mitochondrial sequences identified X contigs as mitochondrial in origin based on high-identity, low e-value hits.


`-outfmt 6` produces tabular output with the following columns:

| Column | Field | Description |
|--------|-------|-------------|
| 1 | qseqid | Query sequence ID |
| 2 | sseqid | Subject (hit) sequence ID |
| 3 | slen | Subject sequence length |
| 4 | length | Alignment length |
| 5 | qstart | Query start position |
| 6 | qend | Query end position |
| 7 | sstart | Subject start position |
| 8 | send | Subject end position |
| 9 | btop |  BLAST top hits |

---

### Output Files

- [mito_contigs.csv](SsFL25-2-1.csv) — CSV list of mitochondrial contigs for NCBI upload

---
## MyGenome Gene Prediction
### Train the data to HMM
```
 scp ske300@mcc.uky.edu:/project/farman_s26abt480/RESOURCES/B71Ref2.fasta snap/B71Ref2.fasta
```
```
 scp ske300@mcc.uky.edu:/project/farman_s26abt480/RESOURCES/B71Ref2_a0.3.gff3
snap/B71Ref2_a0.3.gff3
```
```
echo '##FASTA' | cat B71Ref2_a0.3.gff3 - B71Ref2.fasta > B71Ref2.gff3
```
```
grep '##FASTA' -B 5 -A 5 B71Ref2.gff3
```
```
maker2zff B71Ref2.gff3
```
```
fathom genome.ann genome.dna -gene-stats
```
```
fathom genome.ann genome.dna -categorize 1000
```
```
fathom uni.ann uni.dna -gene-stats
```
```
forge export.ann export.dna
```
```
hmm-assembler.pl Moryzae . > Moryzae.hmm
```
### snap gene predictions

```
snap-hmm Moryzae.hmm  SsFL25-2-1_final00000000.fsa > SsFL25-2-1-snap.zff
```
```
fathom SsFL25-2-1-snap.zff SsFL25-2-1_final00000000.fsa -gene-stats
```
```
snap-hmm Moryzae.hmm  SsFL25-2-1_final00000000.fsa -gff > SsFL25-2-1-snap.gff2
```
####  SNAP predicted genes - 12732
```
 awk '{print $NF}' SsFL25-2-1-snap.gff2  | sort | uniq | wc -l
```
<details>
<summary> Assess Sequence Quality  </summary>  
 
![Augustus](GENE/SNAP.png)

<details>
<summary> Assess Sequence Quality  </summary>  
 
### Augustus gene predictions
```
augustus --species=magnaporthe_grisea --gff3=on --singlestrand=true --progress=true SsFL25-2-1_final00000000.fsa > SsFL25-2-1-augustus.gff3
```
<details>
<summary> Assess Sequence Quality  </summary>  
 
![Augustus](Gene/Augustus.png)
<details>
<summary> Assess Sequence Quality  </summary>  
 
####  Augustus predicted genes - 17703
```
 awk '!/^#/ && $3 == "gene"' SsFL25-2-1-augustus.gff3 | wc -l
```
**IGV SNAP predicted genes visualization:**

![Augustus](GENE/Augustus.png)

### Maker gene predictions
```
singularity exec /share/singularity/images/ccs/MAKER/amd-maker-debian10.sinf maker -CTL
```
```
 sbatch maker.sh SsFL25-2-1_final00000000.fsa
```
```
 gff3_merge -d SsFL25-2-1_final00000000.maker.output/SsFL25-2-1_final00000000_master_datastore_index.log -o SsFL25-2-1_maker.gff3
```
####  Maker  predicted genes - 13175
```
awk '$3== "gene"' SsFL25-2-1_maker.gff3 | wc -l
```

##  predicted genes by SNAP, AUGUSTUS and Maker
<details>
<summary> Assess Sequence Quality  </summary>  
 
![Samegne](GENE/Snap_Augustus_Same.png)
![Samegne](GENE/Snap_Augustus_Different.png)
![All models](GENE/Gene_prediction.png)


