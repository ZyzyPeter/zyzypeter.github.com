---
layout: post
title: 线性代数 伪逆 基变换
categories: linear-algebra
description: 线性代数
keywords: linear-algebra, machine-learning
---

# 线性代数 伪逆 基变换

## 伪逆

### 左逆

#### 定义

当 m*n 列矩阵A为列满秩（ m>n=rank(A) ）矩阵时，A有左逆：
$$
A^{-1}_{left}=(A^TA)^{-1}A^T，\\
且A^{-1}_{left}A=I，所以A^{-1}_{left}被称为左逆
$$

#### 推导

因为A列满秩，所以
$$
A^TA为一个可逆矩阵，因此(A^TA)^{-1}A^TA=I成立
$$

### 右逆

#### 定义

当 矩阵A为行满秩矩阵时，A有右逆：
$$
A^{-1}_{right}=A^T(AA^T)，\\
即AA^{-1}_{right}=I
$$

#### 推导

与左逆相同，故略

### 伪逆

#### 定义

对于 m*n 矩阵 A，若 rank(A) < min(m,n)，则有A的伪逆：
$$
A^+=VΣ^{+}U^T\\
其中V，U分别对应A的奇异值分解UΣV^{T}，Σ^+为Σ的伪逆，且Σ^+Σ=I_0,ΣΣ^+=I_0
$$

$$
其中
I_0=
\left[
\begin{matrix}
	1\\
    &1\\
    &&1\\
    &&&...\\
    &&&&1\\
    &&&&&0\\
    &&&&&&...\\
    &&&&&&&0
\end{matrix}
\right]\\
它的上部分（有1的部分）为单位矩阵，下部分为零矩阵，对应A的零和左零空间
$$

## 基变换

当我们需要为一个矩阵换基以期达到从这个矩阵中抽出一些比较重要的信息特征，从而方便减少矩阵中一些不是很重要的信息的时候就需要用到基变换。

$$
假设目标基（即变换后的基）的向量按列组成矩阵W，那么基变换就是:\\
x=Wc\\
其中x是被变换前的向量，c是被变换后的向量
$$

进行基变换时会发生两件事：
1. 每个向量都会有一组新的坐标，$$$x=Wc$$$就是新旧坐标的关系
2. 每个线性变换都会有一个新的矩阵，$$$B=M^{-1}AM$$$就是新旧矩阵的关系。

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>