---
title: "Checking genome assembly quality using BUSCO"
toc: true
toc_sticky: true
layout: single
permalink: /busco/

excerpt: "Sir, I think we've found them"

gallery:
  - url: assets/images/assembly/busco/acal_busco_euk_percent.png
    image_path: assets/images/assembly/busco/acal_busco_euk_percent.png
    title: "Eukaryota BUSCO counts in A. californicus genome assemblies"
  - url: assets/images/assembly/busco/acal_busco_met_percent.png
    image_path: assets/images/assembly/busco/acal_busco_met_percent.png
    title: "Metazoa BUSCO counts in A. californicus genome assemblies"

gallery_euk:
  - url: assets/images/assembly/busco/busco_plot_eukaryota_odb10.png
    image_path: assets/images/assembly/busco/busco_plot_eukaryota_odb10.png
    title: "Eukaryota BUSCO analysis of A. japonicus genome assemblies - counts"
  - url: assets/images/assembly/busco/busco_plot_eukaryota_odb10_percentage.png
    image_path: assets/images/assembly/busco/busco_plot_eukaryota_odb10_percentage.png
    title: "Eukaryota BUSCO analysis of A. japonicus genome assemblies - percentages"

gallery_met:
  - url: assets/images/assembly/busco/busco_plot_metazoa_odb10.png
    image_path: assets/images/assembly/busco/busco_plot_metazoa_odb10.png
    title: "Metazoa BUSCO analysis of A. japonicus genome assemblies - counts"
  - url: assets/images/assembly/busco/busco_plot_metazoa_odb10_percentage.png
    image_path: assets/images/assembly/busco/busco_plot_metazoa_odb10_percentage.png
    title: "Metazoa BUSCO analysis of A. japonicus genome assemblies - percentages"
 
---

# Introduction
In 2015, a group of scientists published a paper (and tool) about "Benchmarking Universal Single-Copy Orthologs". They called their tool Busco. Which coincidentally enough means "I search" in spanish. They searched through a large number of genomes, identified genes that are single copy orthologs, and then used this database to search newly published genomes as a way to assess the genome assembly quality.  

Let's break this down more - [orthologous genes](https://en.wikipedia.org/wiki/Sequence_homology#Orthology) are genes which share a common ancestry and are a product of speciation. More specifically, the gene sequences diverged through mutations but both originated from the same sequence. This is different from gene duplication events which results in gene [paralogs](https://pubmed.ncbi.nlm.nih.gov/16285863/).

BUSCO only uses genes that are single copy in most genomes. This reduces the risk of a false positive or paralogs being confused for orthologs. Also important is that if a gene is universially found to be single copy, this implies there are some biological/evolution contraints on that genes. Most genomes have undergone duplication events at one time or another. This means that at one point there were multiple copies, but through time all but one copy was lost and this has happened across all organisms. The implications are that these genes are likely to be found in all organisms and could be used as a benchmark of genome assembly quality. 

{% include figure image_path="assets/images/assembly/busco/busco_sampling.png"  caption="Busco distribution. I am honestly not sure how to interpret this graph from the authors website, but it looks significant and interesting. It showing something about how there are single copy genes found in a majority of species." %}

Thus BUSCO has become a ubiqitous tool in genome assembly evaluation. It is to the point where I have heard scientists smuggly throw the term BUSCO around to show they are genome bioinformaticians and are in the "know" as genome assembly is still something of a niche field of bioinformatics. 

While I will be going through how to use the tool and interpret the results, the authors have an excellent [website](https://busco.ezlab.org/) and detailed [user guide](https://busco.ezlab.org/busco_userguide.html) that I recommend you take a look at. 


# BUSCO and MultiQC installation
BUSCO was installed using conda

```bash
conda create -n busco
conda activate busco
conda install -c bioconda busco 
```

Sometimes conda doesn't want to install due to strange reasons. If you run into this, the authors have also created and maintain a docker image. 

If you are running busco across many assemblies, there is a handy tool for compiling the results into one figure. [MultiQC](https://multiqc.info/) is a tool for merging multiple results from a single tool into one figure. It has a module specifically for busco that I will use below. 

```bash
conda create -n multiqc
conda activate multiqc
conda install -c bioconda multiqc 
```

# Running BUSCO analysis

I ran Busco awhile back on three assemblies I generated for the *Apostichopus californicus* genome. The first two used Masurca and platanus-allee which are de-novo hybrid genome assemblers that can be used with short read data. The third assembly was created using redundans and the best sea cucumber genome assembly available at the time as a reference. The short read assemblies both have an N50 less than 10kbp and contain hundreds of thousands of scaffolds/contigs so they are low quality draft assemblies. The redundans assembly is significantly better, but because it used a different species as a reference the assembly content is quite suspect. 

```bash
# For Eukaryote Buscos
# Platanus-allee
busco -c 5 -m genome -i ../out_consensusScaffold.fa -o busco_out_consensusScaffold.fa -l eukaryota_odb10

# MaSuRCA
busco -c 40 -m genome -i ../masurca_p_cali.fasta -o euk_busco_masurca_pcali -l eukaryota_odb10

# Redundans
busco -c 30 -m genome -i ../pcali_ajap.redundans.platanus.fasta -o euk_busco_pcali_ajap.redundans.platanus.fa -l eukaryota_odb10


# For Metazoa Buscos
# Platanus-allee
busco -c 5 -m genome -i ../out_consensusScaffold.fa -o metazoa_busco_out_consensusScaffold.fa -l metazoa_odb10

# MaSuRCA
busco -c 40 -m genome -i ../masurca_p_cali.fasta -o metazoa_busco_masurca_pcali -l metazoa_odb10

# Redundans
busco -c 30 -m genome -i ../pcali_ajap.redundans.platanus.fasta -o metazoa_busco_pcali_ajap.redundans.platanus.fa -l metazoa_odb10
```

Additionally, I ran busco on the published *Apostichopus japonicus* genome assembly and two assemblies generated by Masurca - a low and high confidence one. 
```bash
 caffeinate busco \
 	-i /Users/jon/Documents/genomes/Ajap_genome.fasta \
	-l eukaryota_odb10 \
	-o ajap_euk \
	-m genome \
	-c 3

 caffeinate busco \
 	-i /Users/jon/Documents/genomes/scaffolds.ref.fa \
	-l eukaryota_odb10 \
	-o ajap_masurca_scaffolds_euk \
	-m genome \
	-c 3

 caffeinate busco \
 	-i /Users/jon/Documents/genomes/primary.genome.scf.fasta \
	-l eukaryota_odb10 \
	-o ajap_masurca_euk \
	-m genome \
	-c 3

 caffeinate busco \
 	-i /Users/jon/Documents/genomes/Ajap_genome.fasta \
	-l metazoa_odb10 \
	-o ajap_met \
	-m genome \
	-c 3

 caffeinate busco \
 	-i /Users/jon/Documents/genomes/scaffolds.ref.fa \
	-l metazoa_odb10 \
	-o ajap_masurca_scaffolds_met \
	-m genome \
	-c 3

 caffeinate busco \
 	-i /Users/jon/Documents/genomes/primary.genome.scf.fasta \
	-l metazoa_odb10 \
	-o ajap_masurca_met \
	-m genome \
	-c 3
```

Ignore the caffeinate command. I was having problems installing busco on my server so I decided to run it on my mac laptop. In order to have programs run when the screens goes dark the caffeinate command is used. 

There are no busco databases available specifically for echinoderms. So the next best options are metazoa and eukaryota. I like to run both as the genes used are different between the two datasets. 

# Results

Let's dive into the results. A few things to note though. First, the tools has four categories. Complete buscos includes both duplicated and single copy buscos. Another important detail is that buscos do not represent a best case scenario regarding gene presence or fragmentation. Rather, they represent a rough average for what you can expect to see in your genome assembly. So if you have 80% complete, 15% fragemented, and 5% missing buscos, then you will likely see roughly find that 80% of the genes from your assembly are complete (with some being duplicated), 15% are fragmented, and 5% are likely completely missing. This is based off the assumption that the distribution of BUSCO gene structures follows a normal curve or is at least representative of what you would find in a random genome. Is this true? I don't know and I have asked the authors this question. 

{% include gallery layout="half" caption="BUSCO analysis results (percents) of A. californicus genome assemblies. Left one is Eukaryota, right one is Metazoa" %}

You will recall that the *A. californicus* assembly was created using short paired end reads. So this is actually an impressive results when compared against other short read assemblies. In an ideal world the BUSCO would be in the 90% complete category, however, high 70% to low 80% complete is respectable. This implies to me that the majority of genes are present and complete with a relatively small percent missing or fragmented. This roughly corresponds to what I observed when looking at gene model predictions. In particular I looked at two gene familes HSP70 and PSP94-like and found that they were largely present and complete with a few fragmented or missing. 

It is interesting to note that using a reference genome did improve the results some, but not much. In fact, when compared with the busco results from the reference genome, redundans essentially mirrored it which suggests that it may be introducing artifacts from the reference genome assembly into the redundans assembly. 

Also interesting, masurca did worse than platanus-allee. I recently re-assembled the *A. japonicus* genome using masurca and platanus-allee using short and long read data and the masurca assembly was significantly better than the platanus-allee assembly to the point that I didn't even include it in the below analysis. Only goes to show it is important to try a few different assemblers. 

{% include gallery id="gallery_euk" layout="half" caption=" Eukaryota BUSCO analysis of A. japonicus genome assemblies.  Left one is total count, right one is percentages" %}

I have included both percent and total count here because I think it is important to know how many buscos are actually be searched for. Keep in mind the average genome has 20,000 to 30,000 genes. 

So the original published assembly for *A. japonicus* had quite a few duplicated buscos which is a little suspicious. The high quality assembly looks really good with a much smaller proportion of duplicated buscos but also doesn't have as many complete buscos as the the original. This could be because the assembly is about 100 megabases smaller than the published one. The low confidence assembly is more complete and in fact has no missing eukaryota buscos, but a very large percentage of duplicated buscos. This isn't surprising as masurca probably struggled to figure out what to do with some of heterozygosity in the genome. Apparently it figured it out for a large percentage though for the high confidence assembly. 

{% include gallery id="gallery_met" layout="half" caption="Metazoa BUSCO analysis of A. japonicus genome assemblies. Left one is total count, right one is percentages" %}

The story is largely the same here. The high confidence masurca assembly has the best results, the published assembled has a lot of duplicated buscos and the low confidence assembly has a ton of duplicated buscos but also more complete buscos relative to the other two assemblies. 

Based off this, I am feeling pretty good about the high confidence masurca assembly. It may be quite a bit smaller than the estimated genome size of 900mb and smaller than the 800mb published assembly, but it contains more genes and is higher quality. This is also reflected in the blobtoolkit analysis. 

With that decided, I will probably move onto polishing to remove any assembly errors and then on to gene modeling and annotation. 