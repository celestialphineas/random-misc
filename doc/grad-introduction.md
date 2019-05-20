## 摘要

人类肠道中微生物群的基因组学研究愈发引起研究关注，不断增进着人们对进化和人类健康的理解。将单细胞基因组学的方法应用于其中还方兴未艾，其产生的生物信息数据给计算带来的挑战仍亟待解决。本文我们提出了崭新的数据处理流程，从人类肠道微生物群的测序数据中恢复基因组。该数据由前沿的单细胞实验设计获得。经数据处理，从中发现了共708个基因组组装草图，其中90个完整性超过了90%，61个是高质量的（> 90%完整性，< 5%污染度）。通过聚类分析，我们将细胞划分为组，每组对应于一个物种，基因组组装即由协同组装同物种测序数据得到。明确细胞的物种可以帮助我们更好地刻画微生物群落的结构。我们的数据处理结果也预示着，单细胞方法能帮助人们在细胞层次上更详尽地研究微生物群落。

## Abstract

Microbiome in human gut has increasing become a research interest, providing us with insights into understandings towards evolution and human health. The application of single-cell genomics in this field has remained unfully practiced and requires computational efforts to solve the challenges in handling sequencing data. In this essay, we proposed a new data processing pipeline for recovering genomes in human gut microbiome sequencing data acquired by a novel single-cell experiment design. 708 draft genome assemblies were recovered, with 90 of them having over 90% completeness and 61 in high quality (> 90% completeness, < 5% contamination). The genome assemblies were acquired by co-assembly clustered single-cell sequencing data at the species rank. Identifying the species of the cells can also helped us better characterize the structure of the microbial community. The data analysis result implies that the new single-cell method will allow us to study the microbial community in detail up to the cell level.

## 绪论

### 课题背景

人类肠道中的微生物群（microbiome in human gut）愈发引起研究关注，不断增进着我们对进化和人类健康的理解。基因组学领域的方法也被应用于肠道微生物，例如宏基因组学（metagenomics），是产生于近二十年的基因组学子领域 {{Gritz 2015}}；以及更新近的单细胞方法（single-cell），其使用到了细胞分离和全基因组扩增（whole genome amplification）技术。{{Shapiro 2013}}

现代遗传学和基因组学都需要DNA测序技术，特别是高通量的二代测序（second, or next-generation sequencing）{{Hodkinson 2014}}。测序实验会产生大量的数据，这些数据有不完整性，也有生物化学方法和噪声所引入的人为效应。解决这些问题，无不需要计算方面的努力。利用计算方法解决生物学问题，即生物信息学，在应用到微生物群基因组学时就涉及到算法设计、数据科学方法、工程策略和可视化技术等。这些计算方法让我们得以更好地从实验数据中揭示生物学结论。

本研究致力于从大规模的人类肠道微生物群DNA测序数据中发现基因组。这些DNA测序数据来自于粪便样品，利用先进的生化实验设计获得。在微生物群基因组学研究中，单细胞方法较宏基因组更为少见，因此本文也相应地提供了一套新方法用以处理单细胞测序数据。本文用数据科学方法，着手于计算策略的设计和部分算法实现，呈现了数据处理与分析流程，并得出了相应的结果与结论。

关于核心的生物学概念、二代测序中最为基础的数据处理技术与更加详细的生物学背景，请参看第二部分。本文的综述一章将会侧重于更为上层的数据处理方法与工具的原理和性质。

### 论文研究目标和内容

#### 研究内容

本文我们提出一种分析微生物测序数据的新方法，这些数据来自于单细胞方法获得的人类肠道微生物群。整个数据处理流程旨在更好地刻画人类肠道中的微生物群落。

原始测序数据以一组DNA分子序列的字串形式出现。对微生物群落的刻画将涉及以下步骤：分类学分类（taxonomic classification），利用计算出的特征（这些特征往往有化学或生物学含义）聚类，通过组装（assembly）复原基因组，对组装草图（draft assembly）的质量评估与控制，以及结果的可视化。

基因组发现与更优质的组装、与对单细胞样品中物种对应细胞个数的更好估计，能够增进我们对微生物群落的认识。为全新的单细胞实验开发出这样一套新的计算方法也有助于我们得到新的生物学发现和结论。

#### 主要任务与目标

* 从单细胞测序数据中发现基因组
  * 对单细胞测序结果的分类学分类
  * 协同组装（co-assembly）以得到基因组组装草图
  * 精细化（refine）组装草图
* 对微生物群落的定量描述
  * 物种丰度和统计学性质
  * 对物种间基因组相似度的估计
* 质量评估与控制
  * 质量评估结果的统计与可视化
  * 结果与结论的展示与分析

#### 数据概述

##### 模拟群落（mock community）

我们将实验方法应用于模拟群落作为实验设计和数据分析的基准，并评估实验质量和生化引入的人为效应。这个模拟群落由四个物种组成（枯草杆菌*Bacillus subtilis*, 大肠杆菌*Escherichia coli*, 克雷伯氏肺炎菌*Klebsiella pneumoniae*和金黄色葡萄球菌*Staphylococcus aureus*）。下将数据的关键属性列出：

* 2985个细胞，对应于2985个测序文件.fastq
* 双端（paired-end，参照第二部分中的定义）
* 读长150 bp（碱基对）
* 总量约36 GB

##### NextSeq肠道单细胞数据

来自捐赠者粪便样品中的五个单细胞样品，通过Illumina NextSeq测序。

* 5个样品
* 共3011个有效细胞（样品细胞数：209、462、1056、464、820）
* 双端和单端（single-end）
* 读长150 bp
* 总量约76 GB

##### NextSeq和NovaSeq肠道单细胞数据

来自同一位捐赠者粪便样品中的十个单细胞样品，通过Illumina NextSeq和Illumina NovaSeq获得。

* 10个样品
* 共18903个有效细胞（样品细胞数：2167、2426、2332、2319、1606、1242、2276、2109、1125、1301）
* 双端和单端
* 读长150 bp
* 总量约300 GB

本文处理的单细胞数据为上述两批肠道单细胞数据的总和，共21914个细胞，总的原始数据量约380 GB。

### 论文结构安排

第一章是对课题背景的大致介绍，包括课题的领域背景，该工作的内容和目标。第二章是额外的文献综述，对上层分析用到的计算工具和方法有着较为详尽的介绍，特别是和本文方法息息相关的方法原理与性质。对关键生物学概念的阐释，以及本文所处背景的现状与主要方法在第二部分有着更加详尽的介绍，也包含一篇近期文献节录的翻译，在第一部分不再重复。

第三章详细描述了我们数据处理的方法与流程，首先是对通过单细胞数据恢复基因组的概述，后续各节分别介绍了基于分类学的预分类、细胞聚类和清理操作。第四章则是数据处理的结果与结论，主要内容包括对复原的基因组的质量评估，与统计学分析。在这一章中，我们使用了较多可视化手段，便于读者迅速抓住结果的关键特性，这也是生物学领域常见的结果呈现方式。第五章是讨论与总结，首先我们会指出已知的问题与后续工作，最后列出在本文工作过程中所完成的代码工作。

实验设计、部分中间数据与图表将会放在附录。