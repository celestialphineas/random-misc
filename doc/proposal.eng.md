## Proposal Report

### Background to the Problem

#### Research Background

The incoming data is the DNA sequencing data of over 30,000 cells from two human subjects and one environment sample. The estimated data size is about 5 TB.

The computational question we may ask and try to solve in this background is how to find out most out of the massive single-cell sequencing data. How could the single-cell data help us find more genomes and more complete genomes in microbiome samples? And what computational techniques can we apply to achieve such a goal?

#### Meaning & Objects

Data analyzing of single-cell sequencing data is an important part in the microbiome study, and necessary to biological findings and conclusions. The main objects of the work will be,

* To find genomes in single-cell sequencing data. The completeness of species and the coverage/depth of the genomes are two major goals in data analyzing.
* To improve single-cell sequencing data analyzing workflow. This concerns preprocessing of the sequencing data for better co-assembly strategy. Computational effectiveness and efficiency must be considered.
* To develop necessary tools. Scripts to kick off computational tasks on clusters, to call the available tools must be written. Algorithm selection and implementation are also needed. Visualization techniques to plot graphs will also be performed. Data format reading and converting tools will be developed as well.

### Contents & Methods

#### Research Content

Assembly, clustering, classification, co-assembly and assessment of massive DNA sequencing data acquired by single-cell approach.

#### Techniques

We will use some of the popular tools to help us with the assembly, alignment and assessment. The most important ones are SPAdes (DNA assembler) {{Bankevich 2012}} and Bowtie2 (DNA alignment tool) {{Langmead 2012}}.

We would also use Mathematica and Python as platforms to do further data analysis including clustering, classification and visualization. We would implement or select popular statistical algorithms on these programming languages. Parsers or file processing scripts may also be implemented in various of languages.

#### Feasibility Evaluation

The major challenge of this study’s feasibility is the on the computational resources. With the help of the resources to be mentioned, the computing just becomes feasible.

The computations will be performed on C3DDB (Commonwealth Computational Cloud for Data Driven Biology), a computation cluster opening to research institutes and universities in Massachusetts. With the help of Slurm workload manager, it becomes possible to parallelize computational jobs and allocate computational resources. We are able to get 128 CPUs, 250 GB RAM for each computational job at maximum.

### Planning & Expectation

#### Project Plan

* Late January, project starts, literature reading
* By late February, data analyzing experiments, literature review and project proposal
* March, explorations on data processing methodology and workflow
* April, handling the real data, conclude primarily
* May, data analyzing finished, conclusion and documentation

#### Expected Result

* Organized workflow and good engineering practices of big data analysis.
* A few straight-forward methods to classify the cell data at very first stage for relatively high computational efficiency.
* Genomes at high completeness covered from the raw data.
* Development of reusable tools.

### References

