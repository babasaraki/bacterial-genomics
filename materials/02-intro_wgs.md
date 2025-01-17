---
title: "Introduction to WGS"
---

::: {.callout-tip}
#### Learning Objectives

- Describe differences between sequencing data produced by Illumina and Nanopore platforms. 
- Recognise the structure of common file formats in bioinformatics, in particular FASTA and FASTQ files.
- To introduce participants to the use of FastQC and MulitQC to produce quality reports for Illumina sequences.

:::

## 1.1.1 Next Generation Sequencing

The sequencing of genomes has become more routine due to the [rapid drop in DNA sequencing costs](https://www.genome.gov/about-genomics/fact-sheets/DNA-Sequencing-Costs-Data) seen since the development of Next Generation Sequencing (**NGS**) technologies in 2007. 
One main feature of these technologies is that they are _high-throughput_, allowing one to more fully characterise the genetic material in a sample of interest. 

There are three main technologies in use nowadays, often referred to as 2nd and 3rd generation sequencing: 

- Illumina's sequencing by synthesis (2nd generation)
- Oxford Nanopore, shortened ONT (3rd generation)
- Pacific Biosciences, shortened PacBio (3rd generation)

The video below from the iBiology team gives a great overview of these technologies.

<p align="center"><iframe width="560" height="315" src="https://www.youtube.com/embed/mI0Fo9kaWqo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>

### Illumina Sequencing

Illumina's technology has become a widely popular method, with many applications to study transcriptomes (RNA-seq), epigenomes (ATAC-seq, BS-seq), DNA-protein interactions (ChIP-seq), chromatin conformation (Hi-C/3C-Seq), population and quantitative genetics (variant detection, GWAS), de-novo genome assembly, amongst [many others](https://emea.illumina.com/content/dam/illumina-marketing/documents/products/research_reviews/sequencing-methods-review.pdf). 

An overview of the sequencing procedure is shown in the animation video below. 
Generally, samples are processed to generate so-called _sequencing libraries_, where the genetic material (DNA or RNA) is processed to generate fragments of DNA with attached oligo adapters necessary for the sequencing procedure (if the starting material is RNA, it can be converted to DNA by a step of reverse transcription). 
Each of these DNA molecule is then sequenced from both ends, generating pairs of sequences from each molecule, i.e. _paired-end sequencing_ (single-end sequencing, where the molecule is only sequenced from one end is also possible, although much less common nowadays). 

This technology is a type of _short-read sequencing_, because we only obtain short sequences from the original DNA molecules.
Typical protocols will generate 2x50bp to 2x250bp sequences (the 2x denotes that we sequence from each end of the molecule). 

<p align="center"><iframe width="560" height="315" src="https://www.youtube.com/embed/fCd6B5HRaZ8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>

The main advantage of Illumina sequencing is that it produces very high-quality sequence reads (current protocols generate reads with an error rate of less than <1%) at a low cost. 
However, the fact that we only get relatively short sequences means that there are limitations when it comes to resolving particular problems such as long sequence repeats (e.g. around centromeres or transposon-rich areas of the genome), distinguishing gene isoforms (in RNA-seq), or resolving haplotypes (combinations of variants in each copy of an individual's diploid genome).


### Nanopore Sequencing

Nanopore sequencing is a type of _long-read sequencing_ technology. 
The main advantage of this technology is that it can sequence very long DNA molecules (up to megabase-sized), thus overcoming the main shortcoming of short-read sequencing mentioned above. 
Another big advantage of this technology is its portability, with some of its devices designed to work via USB plugged to a standard laptop. 
This makes it an ideal technology to use in situations where it is not possible to equip a dedicated sequencing facility/laboratory (for example, when doing field work).

![Overview of Nanopore sequencing showing the highly-portable MinION device. The device contains thousands of nanopores embedded in a membrane where current is applied. As individual DNA molecules pass through these nanopores they cause changes in this current, which is detected by sensors and read by a dedicated computer program. Each DNA base causes different changes in the current, allowing the software to convert this signal into base calls.](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41587-021-01108-x/MediaObjects/41587_2021_1108_Fig1_HTML.png?as=webp)

One of the bigger challenges in effectively using this technology is to produce sequencing libraries that contain high molecular weight, intact, DNA. 
Another disadvantage is that, compared to Illumina sequencing, the error rates at higher, at around 5%. 

:::{.callout-note}
**Illumina or Nanopore for Bacteria sequencing?**

Both of these platforms have been widely popular for sequencing bacterial pathogens. 
They can both generate data with high-enough quality for the assembly and analysis of bacterial genomes. PacBio is also used for sequencing Bacterial genomes but we may not have time to talk about it here.
Mostly, which one you use will depend on what sequencing facilities you have access to. 

While Illumina provides the cheapest option per sample of the two, it has a higher setup cost, requiring access to the expensive sequencing machines. 
On the other hand, Nanopore is a very flexible platform, especially its portable MinION devices. 
They require less up-front cost allowing getting started with sequencing very quickly in a standard molecular biology lab.
:::


## 1.1.2 Sequencing Analysis

In this section we will demonstrate two common tasks in sequencing data analysis: sequence quality control and mapping to a reference genome. 
There are many other tasks involved in analysing sequencing data, but looking at these two examples will demonstrate the principles of running bioinformatic programs.
We will later see how bioinformaticians can automate more complex analyses in the [Sequencing Quality Control](../04-sequence_qc/4.1-sequence_qc.md) and [Short Read Mapping](../05-short_read_mapping/5.1-short_read_mapping.md) sections.

One of the main features in bioinformatic analysis is the use of standard file formats.
It allows software developers to create tools that work well with each other. 
For example, the raw data from Illumina and Nanopore platforms is very different: Illumina generates images; Nanopore generates electrical current signal. 
However, both platforms come with software that converts those raw data to a standard text-based format called **FASTQ**. We will briefly describe a couple of these file formats here, but will talk about them in more details in a later session -- Day 2 -- [Common File Formats](../03-file_formats/3.1-file_formats.md)


### FASTQ Files

FASTQ files are used to store nucleotide sequences along with a quality score for each nucleotide of the sequence. 
These files are the typical format obtained from NGS sequencing platforms such as Illumina and Nanopore (after base calling). 

The file format is as follows:

```
@SEQ_ID                   <-- SEQUENCE NAME
AGCGTGTACTGTGCATGTCGATG   <-- SEQUENCE
+                         <-- SEPARATOR
%%).1***-+*''))**55CCFF   <-- QUALITY SCORES
```

In FASTQ files each sequence is always represented across four lines. 
The quality scores are encoded in a compact form, using a single character. 
They represent a score that can vary between 0 and 40 (see [Illumina's Quality Score Encoding](https://support.illumina.com/help/BaseSpace_OLH_009008/Content/Source/Informatics/BS/QualityScoreEncoding_swBS.htm)). 
The reason single characters are used to encode the quality scores is that it saves space when storing these large files. 
Software that work on FASTQ files automatically convert these characters into their score, so we don't have to worry about doing this conversion ourselves.

The quality value in common use is called a _Phred score_ and it represents the probability that the respective base is an error. 
For example, a base with quality 20 has a probability $10^{-2} = 0.01 = 1\%$ of being an error. 
A base with quality 30 has $10^{-3} = 0.001 = 0.1\%$ chance of being an error. 
Typically, a Phred score threshold of >20 or >30 is used when applying quality filters to sequencing reads. 

Because FASTQ files tend to be quite large, they are often _compressed_ to save space. 
The most common compression format is called _gzip_ and uses the extension `.gz`.
To look at a _gzip_ file, we can use the command `zcat`, which decompresses the file and prints the output as text. 

For example, we can use the following command to count the number of lines in a compressed FASTQ file:

:::{.callout}
### Counting lines of a fastq file
First navigate to the working directory
```bash
cd ~/Desktop/workshop_files_Bact_Genomics_2023/01_intro_WGS/
```

Now, copy the below code (*using the copy link to the right of the code*), paste in your terminal and hit <kbd>Enter</kbd> to execute it. Note that it may take a few seconds to run as this is a large file.
```bash
zcat G26832_R1.fastq.gz | wc -l
```
```
12099628
```
:::

If we want to know how many sequences there are in the file, we can divide the result by 4 (since each sequence is always represented across four lines).


### FASTQ Quality Control

One of the most basic tasks in Illumina sequence analysis is to run a quality control step on the FASTQ files we obtained from the sequencing machine. 

The program used to assess FASTQ quality is called _FastQC_. 
It produces several statistics and graphs for each file in a nice `html` report that can be used to identify any quality issues with our sequences. 

![FastQC quality report](../fig/fastqc_quality.png)

We will explore more on *FASTQC* later.


:::::{.callout-important icon=false}
## ***Exercise 1.1.2.1:*** Other files in the `ìntro_WGS` directory

In the course materials directory `~/Desktop/workshop_files_Bact_Genomics_2023/01_intro_WGS` we have other files in addition to the FASTQ.

- Can you tell how many files we have in there using the terminal?
- Go ahead and list the file names.

:::{.callout collapse="true"}
## ***Solution:***
First navigate to the working directory
```bash
cd ~/Desktop/workshop_files_Bact_Genomics_2023/01_intro_WGS/
```

Then use the list `ls` command to list the files there

```bash
ls
```
```
G26832_R1.fastq.gz  MTB_H37Rv.fasta
```
:::
:::::


:::note
**Quality Control Nanopore Reads**

Although FastQC can run its analysis on any FASTQ files, it has mostly been designed for Illumina data. 
You can still run FastQC on base-called Nanopore data, but some of the output modules may not be as informative. 
FastQC can also run on FAST5 files, using the option `--nano`. 

You can also use [MinIONQC](https://github.com/roblanf/minion_qc), which takes as input the `sequence_summary.txt` file, which is a standard output file from the Guppy software used to convert Nanopore electrical signal to sequence calls. 
:::


### Read Mapping

A common task in processing sequencing reads is to align them to a reference genome, which is typically referred to as **read mapping** or **read alignment**. 
We will continue exemplifying how this works for Illumina data, however the principle is similar for Nanopore data (although the software used is often different, due to the higher error rates and longer reads typical of these platforms). 

Again, this is just an introduction, we will delve deeper into it in subsequent sessions.

Generally, these are the steps involved in read mapping (figure below):

- **Genome Indexing |** Because reference genomes can be quite long, most mapping algorithms require that the genome is pre-processed, which is called genome indexing. You can think of a genome index in a similar way to an index at the end of a textbook, which tells you in which pages of the book you can find certain keywords. Similarly, a genome index is used by mapping algorithms to quickly search through its sequence and find a good match with the reads it is trying to align against it. Each mapping software requires its own index, but we **only have to generate the genome index once**. 
- **Read mapping |** This is the actual step of aligning the reads to a reference genome. There are different popular read mapping programs such as `bowtie2` or `bwa`. The input to these programs includes the genome index (from the previous step) and the FASTQ file(s) with reads. The output is an alignment in a file format called SAM (text-based format - takes a lot of space) or BAM (compressed binary format - much smaller file size). 
- **BAM Sorting |** The mapping programs output the sequencing reads in a random order (the order in which they were processed). But, for downstream analysis, it is good to _sort_ the reads by their position in the genome, which makes it faster to process the file. 
- **BAM Indexing |** This is similar to the genome indexing we mentioned above, but this time creating an index for the alignment file. This index is often required for downstream analysis and for visualising the alignment with programs such as IGV. 

![Diagram illustrating the steps involved in mapping sequencing reads to a reference genome. Mapping programs allow some differences between the reads and the reference genome (red mutation shown as an example). Before doing the mapping, reads are usually filtered for high-quality and to remove any sequencing adapters. The reference genome is also indexed before running the mapping step. The mapped file (BAM format) can be used in many downstream analyses. See text for more details.](../fig/ngs_mapping.svg)


### Quality Reports

We've seen the example of using the program _FastQC_ to assess the quality of our FASTQ sequencing files.

What if we have so many FASTQ files and want to view their quality metrics at once.

When processing multiple samples at once, it can become hard to check all of these quality metrics individually for each sample. 
This is the problem that the software _MultiQC_ tries to solve. 
This software automatically scans a directory and looks for files it recognises as containing quality statistics. 
It then compiles all those statistics in a single report, so that we can more easily look across dozens or even hundreds of samples at once. 

Like *FastQC*, _MultiQC_ also generates an html report.
From this report we can get an overview of the quality across all our samples. 

![Snapshot of some of the report sections from MultiQC. In this example we can see the "General Statistics" table and the "Sequence Quality Histograms" plot. One of the samples has lower quality towards the end of the read compared to other samples (red line in the bottom panel).](../fig/multiqc.svg)

For example, from the section "General Statistics" we can see that the number of reads varies a lot between samples. 
Sample ERR5926784 has around 0.1 million reads, which is substantially lower than other samples that have over 1 million reads. 
This may affect the quality of the consensus assembly that we will do afterwards. 

From the section "Sequence Quality Histograms", we can see that one sample in particular - ERR5926784 - has lower quality in the second pair of the read. 
We can open the original FastQC report and confirm that several sequences even drop below a quality score of 20 (1% change of error). 
A drop in sequencing quality towards the end of a read can often happen, especially for longer reads. 
Usually, analysis workflows include a step to remove reads with low quality so these should not affect downstream analysis too badly. 
However, it's always good to make a note of potentially problematic samples, and see if they produce lower quality results downstream.


## 1.1.3. Bioinformatics File Formats

Like we said above, bioinformatics uses many standard file formats to store different types of data.
We have just seen one of these file formats: FASTQ for sequencing reads.

Another very common file format is the FASTA file, which is the format that our reference genome is stored as.
The consensus sequences that we will generate are also stored as FASTA files. 
We detail this format below, but we will explore other [File formats](../03-file_formats/3.1-file_formats.md) in subsequent sessions. 


### FASTA Files

Another very common file that we should consider is the FASTA format.
FASTA files are used to store nucleotide or amino acid sequences.

The general structure of a FASTA file is illustrated below:

```
>sample01                 <-- NAME OF THE SEQUENCE
AGCGTGTACTGTGCATGTCGATG   <-- SEQUENCE ITSELF
```

Each sequence is represented by a name, which always starts with the character `>`, followed by the actual sequence.

A FASTA file can contain several sequences, for example:

```
>sample01
AGCGTGTACTGTGCATGTCGATG
>sample02
AGCGTGTACTGTGCATGTCGATG
```

Each sequence can sometimes span multiple lines, and separate sequences can always be identified by the `>` character. For example, this contains the same sequences as above:

```
>sample01      <-- FIRST SEQUENCE STARTS HERE
AGCGTGTACTGT
GCATGTCGATG
>sample02      <-- SECOND SEQUENCE STARTS HERE
AGCGTGTACTGT
GCATGTCGATG
```

To count how many sequences there are in a FASTA file, we can use the following command:

```bash
grep ">" sequences.fa | wc -l
```

:::::{.callout-important icon=false}
## ***Exercise 1.1.3.1:*** Printing the first 2 lines of a fasta file

We now know how many files are in our current working directory.

Can you write a simple command to display the first two lines of the fasta file?

:::{.callout collapse="true"}
## ***Solution:***

Run this command to view the first two lines of the fasta file

```bash
head -n 2 MTB_H37Rv.fasta
```
```
>NC_000962.3 Mycobacterium tuberculosis H37Rv, complete genome
TTGACCGATGACCCCGGTTCAGGCTTCACCACAGTGTGGAACGCGGTCGTCTCCGAACTTAACGGCGACC
```
:::
:::::


We will see FASTA files several times throughout this course, so it's important to be familiar with them. 

If you are new to the terminal and the command language, don't worry, these exercises were designed to just wet your appetite.

In our next session, we will give you an introduction to the Unix Shell for you to appreciate it better.



## 1.1.4 Credit
Information on this page has been adapted and modified from the following source:

- https://github.com/cambiotraining/sars-cov-2-genomics/blob/main/03-intro_ngs.md