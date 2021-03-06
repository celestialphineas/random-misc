## Literature Review

This section is an additional literature review on the computational aspect of the project with descriptions of algorithms and methods to be used. 

### Clustering

In this section, we will introduce a few related algorithms in cluster analysis and how they were used in our study. This section is organized by aspects of cluster analysis, and not by models of cluster algorithms.

Cluster analysis is a subfield of unsupervised learning. Clustering is a task to group or segment a set of objects into subsets (cluster) by some similarities of the objects {{The Elements of Statistical Learning}}. The elements in each clusters are assigned identical categorical variables. Clustering algorithms can be roughly classified into several groups: connectivity-based (hierarchical), centroid-based, distribution based and a few others. There are also a variety of definitions of distance functions depending on different problems. The number of categorical variables assigned to each object could also be greater than 1, which are known as soft (also, “fuzzy”) clustering methods.

#### Clustering Model

The clustering models used in the study includes centroid-based methods (like *k*-means) and hierarchical clustering methods. We here briefly introduce the latter.

Hierarchical clustering can be implemented either in a bottom-up manner or a top-down manner. Bottom-up wise (agglomerative) hierarchical clustering starts from the points and aggregate to form clusters. Top-down wise (divisible) hierarchical clustering starts from one single cluster and divides to form clusters.

The output of a hierarchical clustering model is often a dendrogram, usually a binary tree with the neighborhood and distance information preserved. The clusters can be identified by splitting the dendrogram.

#### Distance Functions

Besides *p*-norms, Jaccard dissimilarity is also of important use in the study. If $x=\left(x_1,x_2,\dots,x_n\right),y=\left(y_1,y_2,\dots,y_n\right)$ are two vectors of dimension $n$ where $x_i,y_i\in\left\{0,1\right\}$, the Jaccard dissimilarity of $x$ and $y$ is given by

$$J(x,y)=\sum_{i=1}^n x\oplus y$$

The $\oplus$ is the xor operator.

Jaccard dissimilarity is often used as a distance function for binary vectors. Its set version is an indicator to describe how similar two sets are. The Jaccard **similarity** of set $X$ and $Y$ is

$$J(X,Y)=\frac{\left|X\cap Y\right|}{\left|X\cup Y\right|}$$

#### Determining the Number of Clusters

To determining the number of clusters in a dataset is not easy. The centroid-based clustering models require the number of clusters as an input. We also need to know a number for splitting the dendrogram generated using a hierarchical clustering model. A well known method to determine the number of clusters is the elbow method, which is also in use in our study.

The elbow method calculate the variances inside each of the clusters, and generates a plot of the number of clusters vs the total variance under the corresponding clustering result. For hierarchical clustering, this process can be done iteratively on the dendrogram, and we also implemented a snippet for generating the plot on a given dendrogram of the total variance for the elbow method.

{{Elbow image}}

{{Elbow distance matrix. The data is splitted into 25 clusters, which is given by the elbow of the of the variance plot.}}


#### Dimension Reduction & Visualization

Visualization of clustering results concerns plots of the distance matrices, as well as scatter plots involving dimension reduction techniques. Dimension reduction techniques can help us visualize high-dimensional data by reducing the number of dimensions to 1, 2 or 3 which can be simply encoded by spatial positions of the points. Dimension reduction algorithms are also oftentimes used for preprocessing of high dimensional data before feeding the data to the clustering algorithms {{Ding 2004}}. 

We will here take a view of commonly used dimension reduction algorithms and their key properties that we need to concern in our usages.

##### Principal Component Analysis

Principal component analysis (PCA) performs orthogonal transformation to the data, such that the first principal component has the largest variance and the succeeding components have the highest possible variances that satisfy the constraint of orthogonality. Mathematically, PCA can be achieved by eigenvalue decomposition of the covariance matrix or singular value decomposition of the data matrix.

The constraints of PCA provides the good property that its equivalence to *k*-means cluster indicator can be proven {{Ding 2004}}. PCA can be used as a tool of handling high dimensional data before clustering, as well as a  visualization technique that preserves distances on a orthogonal projection.

##### Multidimensional Scaling

Multidimensional scaling (MDS) takes a distance matrix as input, and gives the corresponding points on a given space. This technique can also be used as a way for non-linear dimension reduction.

Let $\left[d_{ij}\right]$ denotes the distance matrix, where $d_{ij}$ is the distance of $i$-th point and $j$-th point, and $x_i (i=1,2,\dots,N)$ denotes the $n$ output points. $\delta_{ij}$ is a dissimilarity of $x_i$ and $x_j$.  The metric multidimensional scaling (mMDS) minimizes the loss function {{Multidimensional scaling}}

$$Strain\left(x_1,x_2,\dots,x_N\right)=\frac{\sum_{i,j}\left(d_{ij}-\delta_{ij}\right)^2}{\sum_{ij}d_{ij}^2}$$

The MDS method preserves the distances to some extent. But it loses some information (the loss) during the embedding to another space.

#### t-SNE

t-stochastic neighbor embedding (t-SNE) is a probabilitic method for dimension reduction. t-SNE defines a probability $p_{ij}$ proportional to the similarity of the high-dimensional objects and the algorithm would learn a mapping that try to minimize the the similarities in the new space.

t-SNE does not preserve the distances and density information well, but the neighbors are preserved to a large extent. {{Schubert 2017}}. This property won’t allow t-SNE to be used for preprocessing of the data to cluster. t-SNE point mapping is by-point learned, and thus when new data is added, there is no fast way to map the addition to the low dimensional space.


### Tools & Methods

#### Mash & Sourmash

Mash is an alignment-free sequence comparison tool based on the MinHash algorithm {{Ondov 2015}}, and sourmash is another implementation of the algorithm {{Brown 2016}}. Both tools give the signatures that work as feature vectors extracted from the input sequences.

The MinHash algorithm is a probabilistic method for quick estimation of the Jaccard similarity of two sets. Comparing to the tretranucleotide frequency (TNF) signature based comparison mentioned in our former review, in which a count of 4-mers is used as a feature, MinHash based method allows us to compare the set of adjacent $k$-mers of a larger $k$. The direct calculation of the similarity of two $k$-mer counts becomes impossible for a large $k$, since the number of dimensions of the count vectors is exponential $4^k$. And as $k$ increases, the $k$-mer count of a limited input will get sparser and sparser. In such cases, using a set of the appearing $k$-mers to model the problem is intuitively almost equivalent to using the count vector, the elements of which are almost all 0 and 1 when $k$ is large enough. In this sense, the MinHash based method can be viewed as an approximation of a $k$-mer count based (such as TNF, where $k=4$) method.

Sourmash use $k=31$ by default. Comparing to the TNF method where $k=4$, MinHash based method can preserve the pattern information of the sequences to a larger extent.

#### Kaiju

Kaiju is a sequence taxonomic classification tool. It can classify reads and contigs and give the sequences taxon predictions. Kaiju works by protein level sequence searching which is more robust than searching by DNA because of the conservativeness and degeneracy of protein sequences. 

Kaiju uses Burrows–Wheeler transformation (BWT) for compressing data base sequences and fast sequence searching. BWT algorithm is also used in sequence alignment tools, including Bowtie and BWA, for fast sequence searching and efficient memory usage. {{Menzel 2015}}

In this study, Kaiju is used as a classification tool for single-cell sequencing data.

#### Other Sequence Classification Tools

We also use the online version of rMLST for taxonomic classification of draft genome assemblies. rMLST is a ribosomal multilocus sequence classification tool, and provide taxonomic predictions with probabilities. {{Jolley 2012}}

#### DAS Tool

DAS Tool is a method to combine and integrate the results of different binning methods. It uses a dereplication, aggregation and scoring strategy to optimize binning result, which increase the number of genome bins, improve the quality of the assemblies and reduce the redundancy. {{Sieber 2018}} We here apply DAS Tool to the metagenomic time series data as a strategy to reduce the binning complexity. 

### Quality Assessment

Quality assessment should be performed after the data analysis process. Genome bin quality indicators include completeness and contamination (redundancy rate to the expected genome size) percentage, mean, median and total length of the contigs, and N50 statistics (the shortest length of the longest contigs that covers 50% of the total genome length), etc.

CheckM is an advanced tool for completeness and contamination estimation using marker genes and the genome’s inferred lineage, and is used as the major tool in our quality control process. {{Parks 2015}}

The quality assessment and control results also requires visualization techniques for providing human readers a better understanding to the key features of the data. 

