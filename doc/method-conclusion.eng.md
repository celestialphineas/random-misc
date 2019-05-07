## Method

### Recovering Genomes

The major challenges for recovering genomes from the single cell sequencing data are as follows:

* Information in each cell is not enough to cover the whole genome of the corresponding species, and thus combining the information of cells of the same species is necessary. The common practice is co-assembly.
* The huge amount of cells brings trouble to a few operations, such as co-assembly and clustering, whose orders of space complexity are usually larger than $O(N)$ and the great RAM consumption makes it infeasible for computing.
* The biochemistry methods brings artifacts to data, including bias of sequencing depth over coverage, contamination and noises. {{Shapiro 2013}}

To achieve the goal with high computational efficiency, we propose a new single-cell data analysis pipeline. The general idea of the genome recovering is to first classify the cells by their taxa, and then co-assembly the sequencing data of the cells that are taxonomically similar. The output of this pipeline is supposed to be a series of draft genome assemblies.

{{Figure: Method sketch}}

### Handling the Unclassified Cells



### Quality Control



## Results

### Recovering Genomes



### Before and After Refinement



### Inside the Unclassified Cells



### Genome Similarity Comparison


