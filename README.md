# GA5 answers - Mohammed Alhamadn

------------------------------------------------------------------------

## Part A: Your project dir, Git repo, and protocol

### 1: Fill in the top of the protocol

<!-- No answer needed here -- we check `protocol.md` directly. -->

### 2: Markdown formatting

<!-- No answer needed here -- we check the formatting of both Markdown files. -->

### 3: Create subdirs, and copy the scripts

``` bash
# Your mkdir command: 
mkdir -p scripts data/ref results
cp -v /fs/ess/PAS3493/share/GA5/scripts/* scripts/
```

### 4: Initialize a Git repository, and commit your first files

<!-- No answer needed here -- we check the repo directly. -->

### 5: Download the reference transcriptome

<!-- Put the download command in `protocol.md`, not here. -->

Code to count the number of transcripts:

``` bash
# Your command, to count them, we can coun lines tha stars wih `>`:
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep -c '^>'
```

The output of the command was:

```         
28624
```

*Answer*: The number of transcripts in the file: **28624**

------------------------------------------------------------------------

## Part B: Read trimming

### 6: Run TrimGalore

<!-- Put the code to run TrimGalore in `protocol.md`, not here. -->

*Answer*: Whether all batch jobs succeeded, and how you checked that (and any problems you encountered, and how you solved them):

I am unsure of the first source of the error, because my screen lagged and I had to reload the page. Then I had an issue finding files for some reason. I asked AI, and we tried to debug using `ls "$fastq_dir"/*_R1.fastq.gz`, which could be because the terminal reloaded. However, we tried the absolute path instead and got those 22 files, then we set the relative path `../garrigos/fastq` manually again in the terminal and got 22. Then we submitted them like we did in the loop for GA4, and got 22 jobs (57109025 all the way to 57109046). After that we checked them with `squeue`, we did this as the success check mentioned in the questions, and anoher diagnostic was to run `grep -L` which suggesed by AI to see if it prints nothing when every log contains the marker (no file is listed as missing it), which is what we saw. I also used `grep -c "Successfully finished script trimgalore.sh"` which gave `:1` for each log, `ls results/trimgalore/*_R1.fastq.gz | wc -l` and `*_R2.fastq.gz` both = 22, `*_val_1_fastqc.html` = 22, and `ls -lh` showed 25M–30M sizes. Moved logs into `results/trimgalore/logs/`. All 22 succeeded; only problem was the unset variable, fixed by re-setting paths and re-checking 22 pairs.

------------------------------------------------------------------------

## Part C: Transcript quantification with Salmon

### 7: Create the Salmon index

Code to check the size of the index dir:

``` bash
# Your command:
du -sh results/salmon_index
```

The output of the command was:

```         
1.1G    results/salmon_index
```

*Answer*: How long it took to create the index, and how you found out:

It took 21 seconds for the script to run and the total job was 28 seconds. I found out from the Slurm log (`results/salmon_index/logs/slurm-salmon_index-*.out`) the script echoes `date` at the start (`Sat Nov  7 11:42:29 AM EST 2026`) and at the end (`Sat Nov  7 11:42:50 AM EST 2026`), so the difference is the runtime. I also watched `squeue -u $USER -l` where the TIME column also matched at `00:22` seconds.

*Answer*: The size of the index dir: **1.1G results/salmon_index**

### 8: Run Salmon for all samples

<!-- No answer needed here -- we check `protocol.md` directly. -->

### 9: The transcripts with the most reads

*Answer*: What the `NumReads` column contains:

``` bash
# Your command:
```

The output of the command was:

```         
```

### 10: The gene of the top transcript

``` bash
# Your command:
```

The output of the command was:

```         
```

*Answer*: The gene that the top transcript belongs to:

### 11: Mapping rates

``` bash
# Your command:
```

The output of the command was:

```         
```

*Answer*: The sample with the lowest mapping rate:

------------------------------------------------------------------------

## Part D: Summarizing all steps with MultiQC

### 12: Update your MultiQC script

<!-- No need to paste the script: we'll look at scripts/multiqc.sh and its Git history directly. -->

### 13: Run MultiQC

*Answer*: For each program, the number of reports that MultiQC found, and why it found this number:

### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate, and whether this matches your answer to question 11:

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you, and why you think these lengths are the most common:

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words):

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions, and whether you agree or disagree with each one, and why:

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed:

------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push

<!-- No answer needed here -- we check your GitHub repo directly. -->

### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: