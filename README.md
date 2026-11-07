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

NumReads is Salmon's estimate of the number of reads (fragments) that mapped to each transcript in that sample, as per the Salmon documentation (<https://salmon.readthedocs.io/en/latest/file_formats.html>), and it is the 5th column of quant.sf, produced by Salmon's expectation-maximization estimation. It is a raw count ***estimate***.

``` bash
# Your command: 
tail -n +2 results/salmon_quant/S63/quant.sf | sort -k5 -nr | head -n 3
```

The output of the command was:

```         
XM_039591534.2  6566    6315.086        14518.656821    24004.889
XM_039573735.1  6445    6194.086        5342.073324     8663.254
XM_039573733.1  6591    6340.086        2876.480244     4774.749
```

### 10: The gene of the top transcript

``` bash
# Your command:
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep -m1 "XM_039591534.2"
```

The output of the command was:

```         
>XM_039591534.2 PREDICTED: Culex pipiens pallens vitellogenin-A1-like (LOC120426760), mRNA
```

*Answer*: The gene that the top transcript belongs to: **LOC120426760** (vitellogenin-A1-like).

### 11: Mapping rates

``` bash
# Your command:
grep -i "mapping rate" results/salmon_quant/*/logs/salmon_quant.log
```

The output of the command was:

```         
results/salmon_quant/S63/logs/salmon_quant.log:mapping rate: 82.4560%
results/salmon_quant/S64/logs/salmon_quant.log:mapping rate: 81.9152%
results/salmon_quant/S65/logs/salmon_quant.log:mapping rate: 79.1055%
results/salmon_quant/S66/logs/salmon_quant.log:mapping rate: 81.9350%
results/salmon_quant/S67/logs/salmon_quant.log:mapping rate: 83.0459%
results/salmon_quant/S68/logs/salmon_quant.log:mapping rate: 78.8070%
results/salmon_quant/S69/logs/salmon_quant.log:mapping rate: 82.1824%
results/salmon_quant/S70/logs/salmon_quant.log:mapping rate: 82.3060%
results/salmon_quant/S71/logs/salmon_quant.log:mapping rate: 79.9047%
results/salmon_quant/S74/logs/salmon_quant.log:mapping rate: 78.2753%
results/salmon_quant/S75/logs/salmon_quant.log:mapping rate: 80.3915%
results/salmon_quant/S76/logs/salmon_quant.log:mapping rate: 82.3396%
results/salmon_quant/S77/logs/salmon_quant.log:mapping rate: 82.3281%
results/salmon_quant/S78/logs/salmon_quant.log:mapping rate: 84.2490%
results/salmon_quant/S79/logs/salmon_quant.log:mapping rate: 80.4061%
results/salmon_quant/S80/logs/salmon_quant.log:mapping rate: 82.1721%
results/salmon_quant/S81/logs/salmon_quant.log:mapping rate: 83.6795%
results/salmon_quant/S82/logs/salmon_quant.log:mapping rate: 78.7291%
results/salmon_quant/S83/logs/salmon_quant.log:mapping rate: 76.1504%
results/salmon_quant/S84/logs/salmon_quant.log:mapping rate: 83.2207%
results/salmon_quant/S85/logs/salmon_quant.log:mapping rate: 76.9540%
results/salmon_quant/S86/logs/salmon_quant.log:mapping rate: 83.0532%
```

*Answer*: The sample with the lowest mapping rate: results/salmon_quant**/S83**/logs/salmon_quant.log:mapping rate: **76.1504%**

------------------------------------------------------------------------

## Part D: Summarizing all steps with MultiQC

### 12: Update your MultiQC script

<!-- No need to paste the script: we'll look at scripts/multiqc.sh and its Git history directly. -->

### 13: Run MultiQC

*Answer*: For each program, the number of reports that MultiQC found, and why it found this number:

-   fastqc: 44 reports because TrimGalore ran FastQC on both R1 and R2 of each of the 22 samples.

-   trim_galore: 44 reports because TrimGalore writes one trimming report per read file.

-   salmon: 22 meta reports, 22 fragment length distributions, and 22 library format counts reports because Salmon ran and output once per sample.

    ```         
    ls -lh results/multiqc/multiqc_report.html
    ```

### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate, and whether this matches your answer to question 11:

I looked at he firs table *General Statistics table* and column aligned %, and I found the same answer for q11 that sample S83 had lowest salmon mapping rate, so yes it is the same.

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you, and why you think these lengths are the most common:

The most common adapter length was about 2 bp, which doesn't surprise me as when I look at `help` for the Trim Galore's default --stringency is only at 1 bp. So I think even very short random matches get trimmed as adapter, and 2 bp matches happen often by chance.

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words):

**Q: What I asked:** I asked Claude to explain two things in my MultiQC report in beginner terms: (1) what the Salmon section is showing with its mapping percentages, and (2) what the "Filtered Reads" numbers from Trim Galore mean. And I stated to explain it simply as I am beginner.

**A: Wha I learned:**

-   Wha is the first curve graph, and it explained that is fragment length distribution. Each colored curve is meant for a sample, colored differently. And if samples overlap and peak a he same location, then this is a good sign. And that means there were prepared well. IF no, then preparation was poorly performed.

-   Flered reads, and it explained that for each sample, how many has survived trimming and and how many removed. The removal can be duo to several points such as low quality, contamination from adapter, and reads they get too short.

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions, and whether you agree or disagree with each one, and why:

Here are my main three chosen feedback from AI about the scripts:

1.  **No `--mem` request on any SBATCH header**

    For his I agree. I went to explain that OOM risk could kill the job and set -e wont catch that. So I **agree** to include it as we learned in class.

2.  **`multiqc.sh` has `#SBATCH` directives but the protocol says to run it with `sbatch`, while the header comment in the script says `Usage: bash scripts/multiqc.sh`**

    AI stated that there is inconsistency for future readers about what we mean to run on compute node or login node. **I partially agree** as it is not something that will need fixing but good catch on not running it containers on login nodes.

3.  **Sample-ID inference uses fragile `basename "$fastq_r1" _R1.fastq.gz` matching with no validation.**

    **I agree.** it's "*fragile"*, but it's the same pattern the course taught us and what we asked to do from GA4. Also all 22 files follow that convention, so it worked here for us.

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed:

------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push

<!-- No answer needed here -- we check your GitHub repo directly. -->

### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: