## Literature Review

### Backgrounds

Sequencing microbiome genome provides insights in understanding biological and medical problems. Microbiome genomic studies enable to achieve tasks like evaluating the abundance of bacteria and spotting the unknown from environmental samples {{Yu 2017}}, human microbiome reconstruction {{Pasolli 2019}}, etc. With these new methods and tools, properties of microbial communities and their connections to environment {{Yu 2017, Seeleuthner 2018}} and human lifestyle {{Pasolli 2019}} can be revealed. Such studies require practicing of the Next-Generation genome sequencing, which concerns technological challenges in both biological and computational aspects. {{Gawad 2015}}

There are two approaches in microbiome genomic studies depending on the genome subjects: (a) metagenome, whose subject is the microbial community in the environment {{Tringe 2005}} and  (b) single-cell, which performs sequencing on isolated individual cells or species. {{Gawad 2015, Tringe 2005}} 

In both cases, the current-state sequencing methods produce data in large scales and advanced algorithms in combinatorics and data science must be employed to make efficient and effective use of the biological data.

#### Biological Background

##### DNA and Genome

DNA (deoxyribonucleic acid) is a type of macromolecule consisting of two chains, forming a double helix structure. Each strand of the DNA molecule is a long sequence built up with nucleotides. One end of the strand is named 5’ end, and the other is named 3’ end. The two strands go opposite directions. There are four types of nucleotides, differentiated by their bases (A, C, G, T). The two chains of the DNA molecule are complementary according to the base pairing rule (A–C, G–T). DNA encodes the genetic information.

The DNA segment that encodes certain protein is called the gene of the protein. The term “gene” also include the non-coding control regions on a DNA molecule. Genome is the whole set of DNA molecules of an organism. {{Understanding Bioinformatics}}

##### The Next-Generation Sequencing

DNA sequencing techniques are applied to determine the nucleotide order in DNA. The very first DNA sequencing technology was as early as 1970s, typically the Sanger Sequencing. Since the mid 1990s, a few new sequencing methods emerged, and these methods are called “next-generation” or “second-generation” sequencing methods.

The DNA sequencing methods are still unable to work out the full DNA sequence as a whole for once, instead, only parts of the DNA can be sequenced each time. The segments directly read by the method are called “reads” in the context.

Read length and the number of reads per run are two factors to assess the performance of a sequencing method. {{Hodkinson 2014}} One of the commercial high-throughput sequencing methods is implemented by Illumina, and is used in the study.

{{Figure needed: IIllumina sequencing}}

The amplification process is necessary for the single molecules to amplify the radioactive or fluorescent tags. The signals are recorded in a series of digital images, the dots are the clusters corresponding to the unique DNA segments. The sequencing results are inferred from the images.

The reads will then be used for data analysis. Common tasks are assembly of the reads to longer contigs (contiguous sequences), alignment to the known genome, binning (combining) the contigs to species level, etc. {{An introduction to Next-Generation Sequencing Technology}} All of the tasks require computational efforts, and will be discussed later.


#### Current Microbiome Genomics Methods

##### Metagenome

The metagenome approach to microbiome studies concerns sequencing 

{{Figure needed: a typical metagenome method work flow}}

##### Single-Cell

{{Figure needed: a typical single-cell method work flow}}

#### Computational Aspects

> Big data, blablabla…

##### Properties of the Input Data

##### Assembly & Alignment

##### Binning

##### Others

> What else?

### Current State

#### Metagenome

| Paper/Method | Number of cells | Cells per sample | Number of reads | Tools |
| ------------ | --------------- | ---------------- | --------------- | ----- |
|              |                 |                  |                 |       |

{{Chart needed: a comparison of the metagenome papers}}

##### Challenges & Limitations

#### Single-Cell

| Paper/Method | Number of cells | Number of reads | Tools |
| ------------ | --------------- | --------------- | ----- |
|              |                 |                 |       |

{{Chart needed: a comparison of the metagenome papers}}

##### Challenges & Limitations

### Discussion

### References



