## 单细胞数据处理

### 复原基因组（Recovering Genomes）

从单细胞测序数据中发现并复原基因组会面临如下挑战：

* 每个细胞测序数据中的信息并不足以覆盖其物种的整个基因组。因此，将来自同一个物种的不同细胞的测序数据信息结合起来就很有必要。最常见的做法是协同组装（co-assembly）。
* 极其大量的细胞会给一些操作带来问题，例如协同组装和聚类，其空间复杂度往往要高于$O(N)$，这会消耗巨大的内存资源，从而难以计算。
* 生化实验方法会在数据中引入人工效应，包括测序深度的选择性偏斜（bias）、污染（contamination）和噪声。{{Shapiro 2013}}

为了让计算更加有效率，我们提出了一种新的单细胞数据分析流程，以尽可能多地复原基因组。其大致的想法是，首先按照细胞的分类单元分组，在每组中按照序列之间的相似度去聚类相似的测序数据，协同组装得到更优质的DNA序列，继续序列比较与聚类并协同组装，最终明确细胞之间的物种界限，协同组装得到基因组。

{{复原基因组流程图}}

### 利用Kaiju对细胞分类

我们希望比较所有细胞之间的相似度，把足够相似的细胞聚合在一起。但这样做涉及到计算细胞之间两两的相似度并聚类，而这项工作不可避免地需要在时间或空间上利用$\Omega(N^2)$的资源。对于可供我们使用的最多240 GB的内存而言，在使用常用工具集的情况下，只能比较不超过7000组序列间的两两相似度。这也是我们做预分类的动机，先将特征差异较大、容易分离的数据分开，然后再利用精细程度更高的方法去聚类与分类。

Kaiju基于查找，可以为测序数据中的每个读分配一个分类单元，而分类级别（rank）可能是域、界、门、纲、目、科、属、种中的任意一级，或是这些标准分类级别之间的某组（group）。尽管Kaiju的输出结果十分精细，部分分类单元可以达到属甚至是种一级，但由于实验、测序和工具本身的局限，其分类结果并不能十分精确地确定细胞的分类单元。其主要问题有：

* 会有一部分无法被分类到任何级别的读，这部分读在我们的数据中占比达到16%
* 随着分类级别越来越细，有这一级别分类单元的读也会越来越少
* 已知、未知和没有分类的比例在不同细胞中是不均匀的
* 每个细胞测序数据中的读所属的分类单元组成会十分复杂，其原因也是多样的，既包括分类工具本身的缺陷，也包括生化实验、测序方法引入的噪声和污染

{{左图：所有读在不同分类级别下的百分比}}

{{右图：在目的分类级别下占比最多的分类单元（包括未知但不包括无分类）在细胞中的占比，细胞频数分布直方图}}

为了尽可能多也尽可能可信地利用分类信息，我们在目这一级别下将细胞分类。这些细胞共属于18个已知的目，也有部分细胞在目一级没有较好的分类单元。

| 编号 | 目                 | 细胞数目 |
| ---- | ------------------ | -------- |
| 1    | Clostridiales      | 14583    |
| 2    | Bacteroidales      | 3490     |
| 3    | Unclassified       | 2836     |
| 4    | Acidaminococcales  | 316      |
| 5    | Coriobacteriales   | 290      |
| 6    | Burkholderiales    | 88       |
| 7    | Eggerthellales     | 76       |
| 8    | Lactobacillales    | 60       |
| 9    | Fusobacteriales    | 56       |
| 10   | Bifidobacteriales  | 51       |
| 11   | Desulfovibrionales | 35       |
| 12   | Erysipelotrichales | 18       |
| 13   | Verrucomicrobiales | 8        |
| 14   | Pasteurellales     | 2        |
| 15   | Veillonellales     | 1        |
| 16   | Streptomycetales   | 1        |
| 17   | Selenomonadales    | 1        |
| 18   | Corynebacteriales  | 1        |
| 19   | Bacillales         | 1        |

在目一级分类的情况下，大多数组的细胞数已经降低到容易计算的水平。但Clostridiales（梭菌目）所对应的细胞数目仍然很多。Clostridiales是人类肠道中十分丰富的一类微生物。为了应对这一问题，我们尝试将Clostridiales向科一级分类：

| 编号 | 科                                        | 细胞数目 |
| ---- | ----------------------------------------- | -------- |
| 1-1  | Unclassified                              | 6639     |
| 1-2  | Ruminococcaceae                           | 3589     |
| 1-3  | Lachnospiraceae                           | 3346     |
| 1-4  | Eubacteriaceae                            | 623      |
| 1-5  | Clostridiaceae                            | 324      |
| 1-6  | Oscillospiraceae                          | 38       |
| 1-7  | Bacteroidaceae                            | 14       |
| 1-8  | Coriobacteriaceae                         | 5        |
| 1-9  | Peptostreptococcaceae                     | 1        |
| 1-10 | Fusobacteriaceae                          | 1        |
| 1-11 | Eggerthellaceae                           | 1        |
| 1-12 | Desulfovibrionaceae                       | 1        |
| 1-13 | Clostridiales Family XIII. Incertae Sedis | 1        |

至此，我们将细胞分为31类，每组的细胞数都能够允许我们做序列比较，这也让我们得以进一步分析。

### 细胞聚类

对以上每一组细胞，我们都利用Sourmash计算其中细胞的两两序列相似度。考虑到每个细胞测序数据的读长，我们选取$k=21,s=500$，即*k*-mer长度为21，产生500个序列计算MinHash（相当于对每个细胞的测序数据提取维度为500的特征向量）。较小的$k$也有助于提升算法的敏感程度。{{Ondov 2015}}

在Sourmash计算出距离矩阵之后，利用scikit-learn工具提供的分层聚类，并分割树状图，使得每个簇内部的最大距离不超过阈值$D$（即完全连接方法，complete linkage）。

如此，上述31组中细胞数大于2的组都将会被细分。我们期望于相同物种的细胞被分在同一组，因此$D$取值应该相对较小，以保证尽可能高的准确率（被分到同一组的都是相同的物种）。但在这一阶段，查全率（所有相同物种的细胞都被分到同一组）并不重要，此时我们对每个细胞所拥有的信息太少，所有相同物种的细胞在这一层次都很难有较高的相似度。因此，我们对同组细胞协同组装，以获得每个物种更多的信息。

{{Burkholderiales的dendro和矩阵，序列比较树形图和不相似度矩阵，这里选取的是Burkholderiales（伯克氏菌目）的26个组装结果，这26个组装结果来自88个Burkholderiales细胞聚类产生的26组}}

在对相似的细胞协同组装后，我们将拥有比原始测序数据的读（read）更长的DNA序列。更多细胞综合起来的信息也相较单独细胞的原始测序数据更多，技术局限引入的测序偏斜（bias）也得以消除。这些都有助于我们接下来更好地对细胞聚类。

{{目前的工作尚无法定量地支持co-assembly对提升序列相似度比较准确性有帮助}}

在获得组装结果之后，我们再次依分类单元，对组装结果做序列相似度比较。随后将相似序列所对应的细胞聚合成新的细胞类，再次协同组装。如此迭代，直至我们观察到较多数目的组装序列大小接近于细菌的基因组大小，一般在3MB左右。{{Brown 2011}}

| #    | 总组数               | 分类学类数         | 序列比较                               | 聚类结果组数 |
| ---- | -------------------- | ------------------ | -------------------------------------- | ------------ |
| 1    | 21914（即细胞数）    | 31（目与梭菌各科） | 按类原始测序数据.fastq，$k=21,s=500$   | 7983         |
| 2    | 7983（最大类有1920） | 13（目）           | 按类的逐组组装序列.fasta，$k=51,s=500$ | 2839         |
| 3    | 2839                 | 1                  | 逐组协同组装的序列.fasta，$k=51,s=500$ | 974          |
| 4    | 974                  | 1                  | 逐组协同组装的序列.fasta，$k=51,s=500$ | 397          |

{{表注：每轮迭代的组数与应用的方法}}

最终我们得到397个细胞组，亦397个协同组装的序列。我们以此作为基因组组装草稿，进行后续分析。

### 清理

在DNA组装的过程中往往会有一些不算太长、质量不高的片段，一个简单的清理方法是设定一个阈值，将长度小于此的片段过滤掉。这里，我们截取每个组装结果中长度大于1 kbp的contig做后续分析 {{Pasolli 2019}}。

#### 质量检查

在获得组装结果后，我们对其做质量检查，主要从完整性（completeness）和污染度（contamination，也就是冗余性，redundancy，即组装结果相比于期望的基因组多出来的比率）两方面评估获得的组装结果质量。

下图呈现了完整性和污染度的整体情形，其中纵坐标经过对数缩放。

{{完整性和污染度图}}

我们选取污染度在20%以上的做下一节的分离清理。

| 组装编号   | 1       | 3       | 11      | 30      | 34      | 20      | 31      |
| ---------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| 完整性 (%) | 100     | 100     | 100     | 100     | 100     | 93.2749 | 95.0813 |
| 污染度 (%) | 729.661 | 456.034 | 311.632 | 273.648 | 223.869 | 143.062 | 133.089 |
| 细胞数     | 1058    | 604     | 339     | 203     | 176     | 278     | 199     |

（续表）

| 2       | 23      | 49      | 29      | 41      | 59      | 38      | 67      |
| ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| 98.2759 | 100     | 97.6489 | 98.1191 | 91.4838 | 78.6981 | 79.877  | 91.8183 |
| 114.232 | 105.731 | 69.7492 | 67.7377 | 54.5873 | 36.4464 | 23.3927 | 21.9352 |
| 618     | 242     | 106     | 215     | 133     | 86      | 150     | 73      |

#### 高污染组装清理

在我们得到的组装结果中，会存有一些污染严重的组装。这很可能是我们将不同种的细胞放在了同一组中进行协同组装的结果，使得它们包含了多个物种的信息。聚类错误的源头在于我们使用的相似度阈值$D$（{{见“细胞聚类”一节}}）可能过大，致使在种这一分类级别上不那么相似的细胞也被聚合在了一起。

这里我们所采取的策略是细分高污染组装所对应的细胞组，试图让来自同一物种的细胞重新被分到同一组。我们利用组装中contig在原测序数据中的平均测序深度作为特征，这一信息称作contig在样品中的丰度（abundance）。测序深度的含义是，将原始测序数据对齐到某一contig上时，该contig的各处对应的读数，平均测序深度即该contig在对应测序数据中平均被多少层读所覆盖，一些文献将这一指标称作覆盖率（coverage）。为不引起混淆，我们此处用“深度”，而“覆盖率”则用来指代被覆盖的contig部分占整个contig的比例。丰度特征被主流的宏基因组contig装箱工具所采用，但其目标是将contig聚类，并将宏基因组组装细分 {{Kang 2015}}。而我们的目标则是利用这个信息对细胞（即对应于宏基因组研究中的样品）聚类。

利用Bowtie2将各个原细胞测序数据对齐到这些细胞的组装上，并整理每个contig在每个细胞中的平均测序深度，设该组装中共有$n$个contig，则对每个细胞$i$，我们也有$n$个对应的测序深度，即一个丰度特征向量$\mathbf{d}_{i}=(d_{i1},d_{i2},\dots,d_{in})^\intercal$，我们利用该特征对每组中的细胞做聚类分析。

由于每个细胞的测序情况不同，各物种基因组不同区域性质不同，我们需要对特征向量的各个维度以及各个向量做正规化。考虑如下矩阵（细胞组中共有$N$个细胞）：

$$\mathbf{D}=\left(\mathbf{d}_1\ \mathbf{d}_2\ \dots\ \mathbf{d}_N\right)=\left(d_{ij}\right)_{n\times N}$$

为了消除各个contig对特征向量贡献的差异，我们首先在二范数下正规化$\mathbf{D}$的每个行向量。为了消除各个细胞信息总量差异的影响，我们再对前述矩阵的每个列向量做规范化。得到$\mathbf{D}'$，其列向量即标准化的丰度特征向量。

对每一个污染度在20%以上的组装，都按照该特征丰度向量聚类。聚类使用的方法是基于欧氏距离，完全连接方式的分层聚类。簇数是在弯折点法和距离矩阵的指导下估计得到的。

在清理过后，我们协同组装细化得到的新的细胞组。

### 基因组组装草图结果

在完成整个数据处理流程与清理后，我们共得到708个基因组组装草图，以俟质量评估。

## 结果与评估

### 测序数据的分类学差异

利用Kaiju对细胞分类时，在目这一分类级别下，细胞的原始测序数据呈现出了较明显的差异。此处，我们用细胞测序数据的四核苷酸频率（TNF, tetranucleotide frequency）之间相似度作为评价原始测序数据差异性的指标。

我们利用*t*-SNE，将每个细胞的标准化的四核苷酸频率统计做降维处理，并将其绘制在二维平面上。在这里，每个点对应于一个细胞。使用Kaiju对细胞的分类结果对图着色时，我们可以很清晰地观察到不同目的细胞在TNF上的差异性、同目细胞在TNF上的一致性。

{{TNF降维图}}

### 基因组组装草图

我们最终获得了708个基因组组装草图，其中有61个高质量的组装（完整性高于90%，污染度低于5%），73个中等质量的组装（完整性高于50%低于90%，污染度低于5%），共90个组装的完整性高于90%，120个组装的完整性高于80%。

{{最终版本的completeness vs contamination图}}

### 细胞组冗余性

属于同一物种的细胞在经过我们的数据处理流程后仍然可能被分到不同的细胞组，产生了同一基因组的不同组装。此处我们来评估这一冗余性。

利用FastANI快速序列比较工具可以更加精准地评估组装之间的相似度。FastANI是一个基于*k*-mer的序列比较工具，相比于MinHash算法，其结果更加接近两个序列的真实ANI（平均核苷酸相似度）{{Jain 2018}}。我们以95%的ANI为界，作为物种与物种之间的界限，将ANI在95%以上的组装归为同一物种。{{Schloissnig 2013}}{{Jain 2018}}{{Pasolli 2019}}

将每个组装对应的与自己来自同一物种的组装数目绘制直方图，观察到对于大多数组装，特别是高质量的组装，与之来自同一物种的组装数只有1或2个。对高质量组装检查时，有10个物种在其中对应于20个组装，另41个组装在物种的核苷酸相似度下属于并无相同。

{{bin redundancy直方图}}

{{bin-tsne，高质量组装所对应的原细胞的TNF特征比较，经*t*-SNE降维绘制在平面上。相似度超过95%的组装上了同样的颜色}}

### 群落结构描述

在微生物基因组学的宏基因组研究中，常常用对数正态分布来建模宏基因组样品中的物种丰度{{Fritz 2019}}。当我们将获得的物种信息对应于每个细胞并加以计数时，能够很好地观察到细胞在样品中的分布仍然是长尾的，但并不能很好地支持成对数正态分布这一结论。

{{每个bin对应细胞数，包含有某细胞数的bin直方图}}

## 结论

单细胞的实验手段在对人类肠道微生物群的基因组学研究中尚属少见。与之相匹配的计算方法和工具也十分匮乏。本文提出了处理单细胞测序数据的崭新方法，从两万多个单细胞的测序数据中恢复了708个基因组组装，其中61个是高质量的，90个组装的完整性超过90%，且我们的组装结果在物种层次上并没有太大的冗余性。这一套方法使得微生物群基因组学的研究可以被细化到细胞层次。

### 讨论

本文工作始终聚焦于如何将相同物种的细胞测序数据分至同一组，这是高效利用单细胞测序数据的，也是本文工作区别于宏基因组数据处理方法（即装箱，binning）的关键差异。为实现这一目标，分类和聚类是使用到的主要工具方法。在目前的数据处理流程中，分类主要利用生物分类学信息，而聚类则涉及到各种特征的使用。本文使用到了诸多具有生物或化学意义的统计与计算特征，利用这些特征我们得以评价DNA序列之间的相似性：

* 统计特征：
  * GC含量（GC content），即序列中G和C核苷酸的占比
  * 四核苷酸频率（Tetranucleotide Frequency, TNF）

* 基于k-mer的相似度估计：
  * MinHash签名，利用Mash距离评估细胞间/细胞组间差异性
  * FastANI对平均核苷酸一致性（ANI）的估计

* 基于序列对齐：
  * 组装所得contig在细胞中的丰度模式
  * 利用序列对齐计算的ANI信息

| 特征        | 特征向量                                     | 规范化                            | 度量                             | 效率 | 备注                                               |
| ----------- | -------------------------------------------- | --------------------------------- | -------------------------------- | ---- | -------------------------------------------------- |
| GC含量      | $x\in(0,1)$                                  | N/A                               | 值                               | 很快 | 只能大致判断一些性质，区别差异较大的物种           |
| TNF         | $\mathbf{v}\in\mathbb{N}^{136}$              | *p*-范数伸缩                      | *p*-范数                         | 较快 | 可以分辨不同的科                                   |
| MinHash签名 | $\mathbf{v}\in\mathbb{N}^{s}$，$s$为Hash数   | N/A                               | Mash距离                         | 快   | 与ANI有较好的相关性                                |
| FastANI     | N/A                                          | N/A                               | ANI估计                          | 中   | 只适用于较为相似、较长的序列                       |
| contig丰度  | $\mathbf{v}\in\mathbb{N}^{n}$，$n$为contig数 | *p*-范数，或选取阈值转换为0-1向量 | *p*-范数，对0-1向量用Jaccard距离 | 慢   | 需要协同组装和对齐，不能直接从序列本身获得这一信息 |
| ANI         | N/A                                          | N/A                               | ANI                              | 慢   | 比较基因组间的差异                                 |

在本文中，我们主要使用Mash距离作为细胞聚类的工具，通过协同组装相似的细胞，不断获得更高质量的物种信息，并继续聚类过程。在获得较多信息和大量质量较好的聚类时，我们利用contig丰度信息处理假阳性部分，这一部分的开销较大。为了将细胞中的序列信息的相似程度可视化出来，我们选取了便于在$\ell^p$空间处理的TNF作为特征。而为了评估两个组装是否属于同一个物种，则使用到了FastANI给出的ANI估计。

在目前的聚类方案中，分割树状图成簇所使用的相似度阈值还较为随意，文中使用了$D=0.95$，即Mash距离在0.95内就会被划分到一簇。在比较接近于基因组大小的组装时，这个阈值是明显偏高的。尽管Mash距离的动机是反映序列本身的相似度，但在引入组装之前，其准确性会因原始测序数据十分零散而受到影响，这是由其原理（基于*k*-mer）所制约的。测序数据中的人为效应对Mash距离的影响会有多大，还需要后续工作的进一步探究。如利用基准数据：模拟群落，来估计同一物种的原始测序数据中Mash距离的分布。改善了的相似度阈值或将有助于减少聚类中的假阳性。

在清理高污染细胞组（即聚类中的假阳性）时，contig丰度分层聚类中，确定簇数也存在着类似问题。目前的策略是利用观察距离矩阵和弯折点法来确定簇数。这一过程需要较多人工失错，难以自动化。

### 后续工作与展望

目前还欠缺将文本所述方法应用到基准数据的测试结果，这项后续工作的意义在于，我们可以进一步探知该方法流程的可靠性，也有助于我们更好地调整该方法流程中各处的策略和参数。

生物信息学上，我们期望于这套崭新的数据处理流程可以最终形成一套自动化和可复用的完整方法甚至是工具。目前我们仍然在实现这一目标的探索之中。

而在回答生物学问题方面，本文的方法在确定每个细胞所属物种上仍相对粗糙。每个被测序的细胞中仍然蕴含着很多信息尚待发现。我们希望单细胞的实验与数据处理方法最终能精准地为我们提供微生物群在细胞层面的各类信息，从而帮助人们更好地认识、理解与建模微生物群落。

### 技术栈与代码贡献

本文工作中的计算密集任务在C3DDB计算集群（Red Hat Enterprise Linux Server release 6.6）上完成。相关的生物信息学工具大多利用Miniconda和pip安装管理，这些工具大多托管在Bioconda频道，部分计算集群上预装的公用环境通过Modules管理，少部分工具通过编译源代码得到可执行程序。部分序列数据处理使用Biopython工具在Python2下编程。利用Mash距离的聚类分析使用scikit-learn {{Pedregosa 2011}}。计算启动脚本用Bash和Python编写。

较小规模的数据处理、数据清理、格式转化、插图绘制等可以在本地处理的工作大多在Mathematica上完成。Mathematica是一个Wolfram语言的交互式编程环境，提供了本文所需的列表处理、符号与数值计算、特征降维、聚类分析、可视化与绘图工具集。本文工作过程中所涉及的重要数据与交互式编程笔记本托管在了一个GitHub仓库上：https://github.com/celestialphineas/bio-notebooks

在工作早期实现了两个Mathematica程序包：

* TNF统计工具：https://github.com/celestialphineas/tnf-count
* .SAM文件解析工具：https://github.com/celestialphineas/MathematicaSAM

