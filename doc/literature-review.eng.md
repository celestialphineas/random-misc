## Literature Review

### Backgrounds

Sequencing microbiome genome provides insights in understanding biological and medical problems. Microbiome genomic studies enable to achieve tasks like evaluating the abundance of bacteria and spotting the unknown from environmental samples {{Yu 2017}}, human microbiome reconstruction {{Pasolli 2019}}, etc. With these new methods and tools, properties of microbial communities and their connections to environment {{Yu 2017, Seeleuthner 2018}} and human health {{Galkin 2018, Pasolli 2019}} can be revealed. Such studies require practicing of the Next-Generation genome sequencing, which concerns technological challenges in both biological and computational aspects. {{Gawad 2015}}

There are two approaches in microbiome genomic studies depending on the genome subjects: (a) metagenome, whose subject is the microbial community in the environment {{Tringe 2005}} and  (b) single-cell, which performs sequencing on isolated individual cells or species. {{Gawad 2015, Tringe 2005}} 

In both cases, the current-state sequencing methods produce data in large scales and advanced algorithms in combinatorics and data science must be employed to make efficient and effective use of the biological data.

#### Biological Background

##### DNA and Genome

DNA (deoxyribonucleic acid) is a type of macromolecule consisting of two chains, forming a double helix structure. Each strand of the DNA molecule is a long sequence built up with nucleotides. One end of the strand is named 5’ end, and the other is named 3’ end. The two strands go opposite directions. There are four types of nucleotides, differentiated by their bases (A, C, G, T). The two chains of the DNA molecule are complementary according to the base pairing rule (A–C, G–T). DNA encodes the genetic information.

The DNA segment that encodes certain protein is called the gene of the protein. The term “gene” also include the non-coding control regions on a DNA molecule. Genome is the whole set of DNA molecules of an organism. {{Understanding Bioinformatics}}

##### The Next-Generation Sequencing

DNA sequencing techniques are applied to determine the nucleotide order in DNA. The very first DNA sequencing technology was as early as 1970s, typically the Sanger Sequencing. Since the mid 1990s, a few new sequencing methods emerged, and these methods are called “next-generation” or “second-generation” sequencing methods.

The DNA sequencing methods do not work out the whole genome for once. Instead, only parts of the DNA can be sequenced each time, and the lengths of the outputs depend on the methods. The direct output sequences of the sequencing procedure are called “reads” in the context.

Read length and the number of reads per run are two factors to assess the performance of a sequencing method. One of the commercial high-throughput sequencing methods is implemented by Illumina, which can reach 300 bp (base-pair) for highest average read length and ~10^9^ reads / run for speed {{Hodkinson 2014}}. The Illumina *de novo* sequencing method (no reference sequence available for aligning the reads) is used in the study. 

{{Figure needed: IIllumina sequencing}}

The amplification process is necessary for the single molecules to amplify the radioactive or fluorescent tags. The signals are recorded in a series of digital images, the dots are the clusters corresponding to the unique DNA segments. The sequencing results are inferred from the images.

The reads will then be used for data analysis. Common tasks are assembly of the reads to longer contigs (contiguous sequences), alignment to the known genome, binning (combining) the contigs to species level, etc. {{An introduction to Next-Generation Sequencing Technology}} All of the tasks require computational efforts, and will be discussed later.


#### Current Microbiome Genomic Methods

##### Metagenome

The metagenome approach to microbiome genomic studies concern sequencing of samples with mixed cells from environmental or artificial {{Yu 2017}} microbial communities. We here draft an outline of a typical metagenome method in a microbiome genomic research.

{{Figure needed: a typical metagenome method work flow}}

##### Single-Cell

Cell sources in single-cell researches may not vary much from the metagenome approach, however the cell isolation and the whole-genome amplification processes must be introduced. {{Gawad 2015}}

{{Figure needed: a typical single-cell method work flow}}

#### Computational Aspects

The next-generation DNA sequencing and the data analysis of it concerns methods in data science. This part sums up what the data to be analyzed looks like, what operations will be performed on the data, how these operations are done, and what are the computational challenges behind them.

##### Properties of the Input Data

* The reads are strings on characters A, C, G and T.
* The reads have overlaps, and this information helps to do the assembly, i.e. combining the short reads to longer sequences.
* Part of the reads are on the complementary strand to the others. For example, if the sequence of a strand is ACCGGGTTTT, then its reverse complementary sequence on the complementary strand is AAAACCCGGT. {{See: Biological Background / DNA and Genome}} And when you are getting reads for 5 bp long, you may see both ACCGG and AAAAC in the result.
* The reads are not always identical to some subsequence of the reference / expected genome, since errors may occur in the replication / amplification of a DNA molecule, as well as in the sequencing process.
* The quality of the sequencing are recorded per nucleotide. See FASTQ file format specification for details. {{Cock 2010}}}
* In metagenome studies, the reads coming from the same run (also in the same file) are from different cells, which brings troubles to assembly. 
* In single-cell studies, the reads are expected to be from the DNA of a single cell. However there might be contamination.

##### Assembly

Assembly is the process to merge short reads to longer sequence in DNA sequencing. The result sequences are called contigs (contiguous segments). The input of the assembly process is a set of sequences with some overlaps, and the expected output is a set of longer sequences, each of which contains some of the input sequences. To achieve the goal, there are two major methods. One is called overlay layout consensus (OLC) and is a very early approach. The other is the de Bruijn graph (DBG) based methods. The de Brujin graph based assembly methods are now most used.

Leaving alone the error correction procedure and the incomplete information that leads to uncontinuity of the result sequence in both methods, a very intuitive formal expression of the assembly problem is to find the shortest common superstring (SCS). SCS problem can be easily reduced to a Traveling Salesman Problem (TSP) and thus is NP-complete. The first step to do the reduction is to model the SCS problem as a graph theory problem, which is also helpful for the design of an approximation algorithm. Since the assembly result itself does not necessarily be SCS of the input reads, such approximation is acceptable and the greedy approach is often practiced. This method is the core idea of OLC assembly.

{{Figure needed: An example of SCS modeling}}

However, computing the overlaps of all input strings is costly, making OLC assembly not efficient enough. De Bruijn graph based methods do not solve the exact SCS problem as well, but it is computationally efficient and practically good enough to merge the input strings. We first collect all substrings of length $k$ in one of the input strings, each is called a $k$-mer. De Bruijn graph is constructed by making all the $k$-mer nodes of a graph (identical $k$-mers are merged into one), and then for each $k$-mer, construct a directed edge right from the corresponding node to the node representing the next $k$-mer in the input string.

{{Figure needed: De Bruijn graph}}

The de Bruijn graphs of each input string is merged together. And a merged superstring (may not strictly be a superstring) can be constructed by making an Euler tour across the nodes. {{Course: Foundations of Computational and Systems Biology}}

Most of the popular DNA assemblers are DBG assemblers with a few adaptations. For example, the SPAdes DNA assembler uses a four-stage algorithm based on multisized de Bruijn graph and results better assembly for single-cell data. {{Bankevich, 2012}}

##### Alignment

The reads may be then aligned to contigs, constructed scaffold or the known gnome. This alignment (finding where the reads from in a larger scale) can be useful for future analysis, for example, evaluating the abundance of certain DNA among samples.

Since the reads are not always identical to the reference sequences. The alignment is not done by precise searching on the reference, rather, by searching by similarity. {{Understanding Bioinformatics}}

##### Binning

The sequencing data of the samples may have a low coverage of the whole genome. Putting the contigs from the same species together can have us make better use of the sequencing data. The contigs may come from either the same sample or across samples. To be concise, binning is to collect similar DNA sequences together to species-level.

How do we know if a contig is from the same cell / species? We can either find clues from the abundance pattern across samples, or make use of the sequences themselves. MetaBAT is an effective tool to do binning, and it introduces a novel way to classify the contigs across samples. Before MetaBAT, most binning are unsupervised, typically clustering by the abundance pattern and sequence features. However, for MetaBAT, the similarity of the contigs are assessed using a probabilistic model. The probability that two contigs are from the same cell is predicted with a naive Bayes classifier, which is trained with data. The later binning process for MetaBAT also uses unsupervised clustering method. {{Kang 2015}}

Most binning tools use a statistic of $k​$-mer frequencies as a way to extract sequence features. {{Sczyrba 2017}} Binning requires various dimension reduction techniques to make classifying and clustering possible or to visualize the data on a 2D plot.

##### Others

There are also other analysis procedures and methods depending on various objects of different studies, but that’s beyond the scope of this background introduction.


### Current State

#### Metagenome



| Paper/Method | Number of cells | Cells per sample | Number of reads | Description | Tools |
| ------------ | --------------- | ---------------- | --------------- | ----------- | ----- |
|              |                 |                  |                 |             |       |

{{Chart needed: a comparison of the metagenome papers}}

##### Challenges & Limitations

#### Single-Cell

| Paper/Method | Number of cells | Number of reads | Coverage & Depth | Description | Tools |
| ------------ | --------------- | --------------- | ---------------- | ----------- | ----- |
|              |                 |                 |                  |             |       |

{{Chart needed: a comparison of the single-cell papers}}

##### Challenges & Limitations

### Discussion



### References



