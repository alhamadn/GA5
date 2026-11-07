# RNA-seq analysis of *Culex pipiens*

-   Author: Mohammed Alhamadn
-   Date created: 2026-11-07
-   Date last modified: 2026-11-07
-   This was run at the `Pitzer` cluster of the Ohio Supercomputer Center ([https://www.osc.edu](#0){.uri})
-   OSC working dir: `/fs/ess/PAS3493/people/alhamadn.1/GA5`
-   GihostnametHub repository: <https://github.com/alhamadn/GA5>

## Prerequisites

-   **Input files**:

    -   Paired-end RNA-seq reads are in `../garrigos/fastq`

-   **Software**:

    -   The scripts run programs using Apptainer containers, with links contained inside the scripts.
    -   If running at OSC, no software installations are needed.
    -   If you're not at OSC, you need to have Apptainer and Slurm installed.

-   Run all code from the working dir listed above. You don't need to create the output dirs, because the scripts will do so.

-   Each script writes a Slurm log file (`slurm-<script>-<jobID>.out`) to the working dir. After the jobs of a step have finished, check these logs for errors, then move them into a `logs/` dir within that step's output dir.

## Set up: Download the reference transcriptome FASTA file from NCBI

``` bash
# Downloaded on 2026-11-07
wget -P data/ref https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/016/801/865/GCF_016801865.2_TS_CPP_V2/GCF_016801865.2_TS_CPP_V2_rna.fna.gz
```

## Set up: Set file paths

``` bash
# Inputs:
fastq_dir=../garrigos/fastq
ref_transcripts=data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz

# Base output dir:
outdir=results
```

## Step 1: Read trimming with TrimGalore

`trimgalore.sh` runs TrimGalore on the paired-end FASTQ files of 1 sample, and renames the trimmed FASTQ files to the same names as the input files. It takes 3 arguments: the R1 FASTQ file, the R2 FASTQ file, and the output dir.

``` bash
for fastq_r1 in "$fastq_dir"/*_R1.fastq.gz; do
    fastq_r2=${fastq_r1/_R1/_R2}
    sbatch scripts/trimgalore.sh "$fastq_r1" "$fastq_r2" results/trimgalore
done
```

## Step 2: Creating a transcriptome index with Salmon

`salmon_index.sh` builds a Salmon index from the reference transcriptome, and takes 2 arguments 1- the reference transcript FASTA file, and 2- the output dir.

``` bash
 sbatch scripts/salmon_index.sh "$ref_transcripts" results/salmon_index
```

## Step 3: Pseudo-alignment and quantification with Salmon

`salmon_quant.sh` runs Salmon in pseudo-alignment mode for 1 sample, and takes 4 arguments: the R1 FASTQ file, the R2 FASTQ file, the Salmon index dir, and the output dir.

``` bash
for fastq_r1 in results/trimgalore/*_R1.fastq.gz; do
    fastq_r2=${fastq_r1/_R1/_R2}
    sbatch scripts/salmon_quant.sh "$fastq_r1" "$fastq_r2" results/salmon_index results/salmon_quant
    done
```

## Step 4: Summarizing the results of all steps with MultiQC

`multiqc.sh` runs MultiQC once for all outputs of the previous steps. It takes 2 arguments: the base output dir and the MultiQC output dir.

``` bash
 sbatch scripts/multiqc.sh results results/multiqc
```