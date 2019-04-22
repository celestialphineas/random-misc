## Literature Review

### Clustering

In this section, we will introduce a few related algorithms in cluster analysis and how they were used in our study. This section is organized by aspects of cluster analysis, and not by models of cluster algorithms.

Cluster analysis is a subfield of unsupervised learning. Clustering is a task to group or segment a set of objects into subsets (cluster) by some similarities of the objects {{The Elements of Statistical Learning}}. The elements in each clusters are assigned identical categorical variables. Clustering algorithms can be roughly classified into several groups: connectivity-based (hierarchical), centroid-based, distribution based and a few others. There are also a variety of definitions of distance functions depending on different problems. The number of categorical variables assigned to each object could also be greater than 1, which are known as soft (also, “fuzzy”) clustering methods.

#### Clustering Model



#### Determining the Number of Clusters

Elbow method


#### Visualization

Visualization of clustering results concerns plots of the distance matrices, as well as scatter plots involving dimension reduction techniques. Dimension reduction techniques can help us visualize high-dimensional data by reducing the number of dimensions to 1, 2 or 3 which can be simply encoded by spatial positions of the points. Dimension reduction algorithms are also oftentimes used for preprocessing of high dimensional data before feeding the data to the clustering algorithms {{Ding 2004}}. 

We will here take a view of commonly used dimension reduction algorithms and their key properties that we need to concern in our usages.

##### Principal Component Analysis

Principal component analysis (PCA) performs orthogonal transformation to the data, such that the first principal component has the largest variance and the succeeding components have the highest possible variances that satisfy the constraint of orthogonality. Mathematically, PCA can be achieved by eigenvalue decomposition of the covariance matrix or singular value decomposition of the data matrix.

The constraints of PCA provides the good property that its equivalence to *K*-means ($K=2$) can be proven {{Ding 2004}}. PCA can be used as a tool of handling high dimensional data before clustering, as well as a  visualization technique that preserves distances on a orthogonal projection.

##### Multidimensional Scaling

Multidimensional scaling (MDS) takes a distance matrix as input, and gives the corresponding points on a given space. This technique can also be used as a way for non-linear dimension reduction.

Let $\left[d_{ij}\right]$ denotes the distance matrix, where $d_{ij}$ is the distance of $i$-th point and $j$-th point, and $x_i (i=1,2,\dots,N)$ denotes the $n$ output points. $\delta_{ij}$ is a dissimilarity of $x_i$ and $x_j$.  The metric multidimensional scaling (mMDS) minimizes the loss function {{Multidimensional scaling}}

$$Strain\left(x_1,x_2,\dots,x_N\right)=\frac{\sum_{i,j}\left(d_{ij}-\delta_{ij}\right)^2}{\sum_{ij}d_{ij}^2}$$

The MDS method preserves the distances to some extent. But it loses some information (the loss) during the embedding to another space.

#### t-SNE

t-stochastic neighbor embedding (t-SNE) 






### Tools & Methods

#### Mash & Sourmash



#### Kaiju



#### Other Sequence Classification Tools



#### DAS Tool

