---
layout: post
title: 机器学习 第十七章 层次聚类和密度聚类
categories: machine-learning
description: 机器学习
keywords: linear-algebra, Theories of probability and statistics, machine-learning
---

## 密度聚类

密度聚类全称**基于密度的聚类（density-based clustering）**，此类算法的假设是**聚类结构能通过样本分布的紧密程度来确定**。通常情况下该算法的原理是通过密度聚类算法考查样本之间的可连接性，并基于可连接样本不断扩展聚类簇以获得最终聚类结果。

### DBSCAN

密度聚类算法**DBSCAN（Density-Based Spatial Clustering of Applications with Noise）**基于一组**邻域（neighborhood）**参数（$$$\epsilon,MinPts$$$）来刻画样本分布的紧密程度。

给定数据集$$$D=\{x_1,x_2,...,x_m\}$$$，定义以下概念

* “**$$$\epsilon$$$-邻域**”：对$$$x_j∈D$$$，其$$$\epsilon$$$-邻域包含样本集$$$D$$$中与$$$x_j$$$的距离不大于$$$\epsilon$$$的样本，即$$$N_\epsilon (x_j)=\{x_i ∈ D | dist(x_i,x_j) ≤ \epsilon\}$$$（$$$dist(x_i,x_j)$$$表示样本$$$x_i$$$和$$$x_j$$$的欧氏距离）

* **核心对象（core object）**：若$$$x_j$$$的$$$\epsilon$$$-邻域至少包含$$$MinPts$$$个样本，即有$$$|N_\epsilon (x_j)|≥ MinPts$$$,则$$$x_j$$$是一个核心对象

* **密度直达（directly density-reachable）**:若$$$x_j$$$位于$$$x_i$$$的$$$\epsilon$$$-邻域中，且$$$x_i$$$是核心对象，那么就称$$$x_j$$$由$$$x_i$$$密度直达（密度直达关系通常不满足对称性）

* **密度可达（density-reachable）**：对$$$x_i$$$与$$$x_j$$$，若存在样本序列$$$p_1,p_2,...,p_n$$$其中$$$p_1=x_i,p_n=x_j$$$，且$$$p_{i+1}$$$由$$$p_i$$$密度直达，则称$$$x_j$$$由$$$x_i$$$密度可达。（密度可达关系满足直递性，但依然不满足对称性）

* **密度相连（density-connectded）**：对$$$x_i$$$与$$$x_j$$$，若存在$$$x_k$$$使得$$$x_i$$$与$$$x_j$$$均由$$$x_k$$$密度可达，则称$$$x_i$$$与$$$x_j$$$密度相连。（密度相连关系满足对称性）

西瓜书提供了上述几个概念的直观显示图

![](\images\blog\ml17-1.png)

基于这些概念，DBSCAN定义“簇”为：**由密度可达关系导出的最大的密度相连样本集合**。用数学语言表示，即：

给定邻域参数$$$(\epsilon,MinPts)$$$，簇$$$C \subseteq D$$$是满足以下性质的非空样本子集：

* **连接性（connectivity）**：$$$x_i ∈ C,x_j∈C \rightarrow x_i$$$与$$$x_j$$$密度相连
* **最大性（maximality）**：$$$x_i ∈ C,x_j$$$由$$$x_i$$$密度可达 $$$\rightarrow$$$ $$$x_j∈C$$$

（D中不属于任何簇的样本被认为是噪声或者异常（anomaly）样本）

可以证明，当$$$x$$$为核心对象时，由$$$x$$$密度可达的所有样本组成的集合$$$X=\{x'∈D|x'由x密度可达\}$$$ 即为满足连接性与最大性的簇。

因此，有了如下DBSCN算法：

![](\images\blog\ml17-2.png)

其中算式“A/B”表示从集合A中除去集合B包含的元素。

显然，DBSCN算法的关键在于如何决定邻域参数$$$\{\epsilon,MinPts\}$$$ 。

## 层次聚类

**层次聚类（hierarchical clustering）**通过在不同层次对数据集进行划分来形成一个树形聚类结构。数据集的划分有**自底向上**的聚合策略和**自顶向下**的分拆策略。

**AGNES（AGglomerative NESting）**便是一种功能采用自底向上聚合策略的层次聚类算法,原理就是先将数据集中的每个样本看作是一个初始聚类簇，然后再在算法运行的每一步中找出距离最近的两个聚类簇进行合并，不断重复这个过程直到达到预设的聚类个数（就像癌细胞扩散那样）。

这个算法的关键就在于如何计算两个样本集合之间的距离，计算单个样本之间的距离很简单，而要计算集合，一种常用的集合间距离的计算方式为**豪斯多夫距离（Hausdorff distance）**：

假设有样本集合$$$X$$$和$$$Z$$$，它们之间的豪斯多夫距离：

$$
dist_H(X,Z)=max(dist_h(X,Z),dist_h(Z,X))\\
其中,dist_h(X,Z)=max_{x∈X}min_{z∈Z}||x-z||_2
$$

或者用这样的计算方式：

![](\images\blog\ml17-3.png)

当聚类距离分别由$$$d_{min},d_{max},d_{avg}$$$计算时，AGNES算法又被称为**单链接（single-linkage）**，**全链接（complete-linkage）**，**均链接（average-linkage）**算法。

其算法步骤如图描述

![](\images\blog\ml17-4.png)

资料参考来源：

清华大学出版社 周志华 《机器学习》 第九章

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>