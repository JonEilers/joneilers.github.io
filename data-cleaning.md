---
title: "Sequence Data Analysis and QC"
permalink: /data-cleaning/
layout: single

toc: true 
toc_sticky: true

header:
  overlay_image: /assets/images/home/cuke1.jpg

excerpt: "Garbage in, garbage out."

sidebar:
  nav: sidebar-main

gallery:
  - url: /fastqc/
    image_path: /assets/images/blobtoolkit/californicus_masurca.snail.png
    title: Apostichopus californicus Masurca Assembly
---

# Introduction

 Each sequencing technology introduces it own unique artifacts and inaccuracies into the sequencing data. Identifying, removing the artifacts, and then analyzing the raw data are the first steps in preparing the sequence data for assembly. If this is the first time dipping your toes into bioinformatics check out this introduction to getting set-up for genome bioinformatics

 **[A brief introduction to what you will need if you are serious about getting into bioinformatics](/setup/)**
 {: .notice--info}

# Checking Data Integrity

 Let's start from the beginning. When you first acquire your data whether it is via a sketchy flashdrive or downloaded from the internet, you will want to check the data integrity. The easiest way is to perform something called a [checksum](https://en.wikipedia.org/wiki/Checksum). This essentially checks that the calculated hash value for the copied data is the same as the calculated hash value for the original data, this verifies that the copied data has not been corrupted. The popular algorithm to use is [md5](https://en.wikipedia.org/wiki/MD5) and is fairly easy. See below link below for a more indepth look at how to do this. 

**[Using Md5sum to verify data integrity](/checksum/)**
{: .notice--info}


# Summary Statistics and Data Quality
Once you can have confidence in the data integrity, it's time to get some rough ideas about what the data looks like. A few things need to be checked: basic statistics such as number of reads and read length, adapter contamination, read quality, overrepresented sequences etc. Thankfully, there are a plethora of tools out there for doing just this. One popular one is called Fastqc and works great for short read data. Additional there has been a surge in tools for assessing long read data quality too. Data types such as Hi-C, while technically sequenced using illumina, require special handling as the data is structured differently from standard short reads. See below for a link to pages that go into more detail on how to check for adapter contamination, read summary statistics and quality, and what to look for. 

**[Checking Illumina data quality using FastQC](/short_read_quality/)**
{: .notice--info}    
**[Checking Long read data quality using Nanoplot and SequelTools](/long_read_quality/)**
{: .notice--info}   
(To Do) [Checking Hi-C data quality using pairtools, qc3C, and HiCExplorer](/hic_read_quality/)   
{: .notice--warning}

# Trimming, Filtering, and Error Correcting Raw Reads
In some cases, there may be problems with the raw sequence data and it needs some cleaning. This can be due to excessive adapter contamination, way too many duplicated sequences, a significiant proportion of the reads are low quality, etc. It is also important to consider how these reads will be used. In some cases you may be able to get away with simply ignoring read quality problems. There are a handful of publications that have looked at how read trimming impacts computation downstream in the pipeline such as during [genome assembly](https://www.mdpi.com/2073-4425/10/10/737). A [paper](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0085024) from 2013 looked at how trimming impacts different data types and their ultimate uses. In this paper, they note that read trimming negatively impacts assembly quality if the trimmming was performed on a high quality dataset. Additionally, some tool specifically say to not perform read trimming. This is the case with the genome assembler [MaSURCA](https://github.com/alekseyzimin/masurca). With long reads from Pacbio or ONT an additional step can be performed called error correcting in which the long reads are used to correct sequencing errors in each before being used for genome assembly. 

**[Raw sequence data trimming and filtering](/trimming/)**
{: .notice--info}
(To Do) [Error correcting long read data](/long_read_error_correction/)
{: .notice--warning}
(To Do) [Trimming and filtering Hi-C data](/hic_data_trimming/)
{: .notice--warning}

# K-mer Analysis of Reads
Now the data is looking good, all the reads have high phred scores and there appears to be little to no adapter contamination. It is time to take a closer look at the raw sequence data. There are a number of tools for doing this, most relay on the analysis of [K-mers](https://en.wikipedia.org/wiki/K-mer) to estimate different aspects of the data. K-mers are essentially sub-sequences or sub-strings and the distribution of these sub-sequences in the data can be used to estimate parameters. One example is in [choice of k-mer size](https://en.wikipedia.org/wiki/K-mer#Choice_of_k-mer_size) when assembling genomes. Larger k-mer sizes result in more memory usage, but improved genome assembly contiguity. Thus it can be important to find the optimal k-mer size for genome assembly. [Kmergenie](http://kmergenie.bx.psu.edu/) can be used to do this. See below for an example. 

**[Estimating Best Genome Assembly K-mer size using Kmergenie](/kmergenie/)**
{: .notice--info}

However, some genome assemblers, such as [MaSURCA](https://github.com/alekseyzimin/masurca) do not require a k-mer size as input, but rather requires an estimated genome size. If there is no genome size estimate available, the genome size can be estimated using k-mer analysis. See below for an example.

**[Estimating genome characteristics using Jellyfish and GenomeScope](/genomescope/)**
{: .notice--info}

Additionally, k-mer analysis can be used to check for various types of contamination in the raw sequence data. The [K-mer Analysis Toolkit (KAT)](https://kat.readthedocs.io/en/latest/index.html) can be used to check for GC bias which might indicate different kinds of contamination such as from PCR. KAT has a number of differents for analyzying the k-mer distribution and what it might indicate. See below for some examples 


**[K-mer Analysis using KAT](/kat/)**
{: .notice--info}

If everything looks good or has been cleaned up, the next step is [genome assembly](/assembly/)! 

