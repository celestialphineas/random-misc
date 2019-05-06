## Introduction

### Background

Microbiome in human gut has increasingly become a research interest, providing us new understandings towards evolution and health. Methods in the field of genomics have been applied to the gut microbiome, such as metagenomics, a subfield of genomics which has came into being for almost two decades {{Gritz 2015}}, and the more recent single-cell approach featuring cell isolation and whole genome amplification from single DNA molecules. {{Shapiro 2013}}

The modern genetics and genomics make use of the new DNA sequencing methods, notably the high-throughput next-generation sequencing. {{Hodkinson 2014}} The tremendous amount of data generated, incompleteness and artifacts due to biochemistry, noises, etc. require computational efforts in problem-solving. The computational aspect of solving biological problems, known as bioinformatics, concerns algorithm design, data science methods, engineering strategies and visualization techniques for the field of microbiome genomics. These methods are necessary to make revealing biological conclusions possible with the data produced by the experiments.

This study discovers genomes from the massive single-cell human gut microbiome DNA sequencing data of stool samples acquired by the state-of-the-art experiment design {{TO BE EXPANDED, WITH NUMBERS}}. We here highlight the single-cell approach for handling the gut microbiome, which is not yet as popular as the metagenomic approach. And  accordingly, a new method for handling the single-cell microbiome genomic data is sketched. The thesis will present the data processing and analysis pipeline with application of data science methods, computational strategy design and a few algorithm implementation, as well as the results and the conclusions we reached.

For detailed descriptions about the biological concepts, backgrounds, computational tasks and methods involved, please refer to part II of the thesis and then the additional review chapter.

### Scope & Objectives

#### Subject of the Research

In the thesis, we introduce a new data analysis pipeline applied to microbiome genomics sequencing data acquired by novel single-cell experiment methods. The data analysis helps to better characterize the microbiome community in human guts. 

The sequencing data is sets of DNA molecule subsequences gathered by isolated cells. Characterization of the microbiome community requires taxonomic classification, clustering by computational features with chemical or biological meanings, discovering genomes by assembly, quality control of the draft assemblies and visualization of the results for better describing the structure of the community.

The better quality of the recovered genome assemblies and the estimation for the numbers of species in single-cell samples will provide us with insights into the properties of the microbiome community. The development of such computational method for processing data of the novel single-cell experiment will also lead to new biological discoveries and conclusions.

#### Major Tasks

- Discovering genomes from single-cell sequencing data
  - Taxonomic classification of the single-cell sequencing
  - Co-assembly to get genomes’ draft assemblies
  - Refining the draft assemblies
- A quantity description of the microbiome community
  - Species abundance and statistical properties
  - Estimation of the genome similarity of the species
- {{INCLUDE METAGENOMICS IF POSSIBLE}}
- Quality control and assessment
  - Quality check measurements statistics and visualization
  - Comparing the results of single-cell, metagenomics and combined methods {{IF POSSIBLE}}

#### A Profile of the Analyzed Data

##### Mock Community

A mock community consisting of 4 species (*Bacillus subtilis*, *Escherichia coli*, *Klebsiella pneumoniae*, and *Staphylococcus aureus*) is used as a benchmark for the experiment design and data analysis for evaluating the quality and biochemistry artifact of the single-cell sequencing data. Below is a list of the key properties of the data.

* 2985 cells
* Paired-end (refer to Part II for the definition)
* 150 bp read length
* ~ 36 GB in total

##### NovaSeq Single Cell Data

Illumina NovaSeq sequencing is performed on 5 single-cell samples acquired from the donor, numbered S1 to S5.

* 5 samples
* 3011 cells
* Paired-end and single-end reads
* 150 bp read length
* ~ 76 GB in total

##### NextSeq Single Cell Data

Illumina NextSeq sequencing is performed on 10 single-cell samples acquired from the same donor.

* 10 samples (with one shared sample with NovaSeq)
* 18903 cells
* Paired-end and single end reads
* 150 bp read length
* ~ 300 GB in total

##### Metagenomics Data

{{OMITTED?}}

### Structure of the Thesis

In Chapter 1, we introduced the general background, subject and the target of the study. Chapter 2 is an additional literature review, with a comprehensive description of the underlying computational tools and methods used in our data analysis pipeline. The biological background and concept clarification are much more detailed in Part II of the thesis and shall not be repeated in Part I again. 

Chapter 3 is a detailed description of the data analysis method. {{SECTION CONTENTS}} The results are shown in Chapter 4, with both the quality assessment and statistical characterizations of the discovered genomes and the comparison of the genomes with taxonomic information. Visualization techniques are used as key methods for presenting the results. Chapter 5 is a summarization, starting with a list of programs finished during the work of this thesis, and then pointing out the known issues and future work.

The design of the experiment together with other supplementary materials, including data, graphs and texts are found in the appendix.