---
layout: post
title: 线性代数 矩阵乘法与可逆矩阵
categories: linear-algebra
description: 线性代数中矩阵的乘法与可逆矩阵
keywords: linear-algebra,machine-learning
---

## 矩阵乘法

计算两个矩阵的乘法有多种方法，我们设有 m x n 矩阵A和 n x p 矩阵B，两矩阵相乘有A·B=C，C是一个m x p矩阵

### 行列内积

对于C矩阵中的第i行第j列元素，有：

$$
c_{ij}=row_i·column_j=\sum^n_{k=i}a_{ik}b{kj}
$$

示例:

$$
\left[
	\begin{matrix}
    a_1&b_1\\
    c_1&d_1
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]
=
\left[
	\begin{matrix}
    a_1a_2+b_1c_2&a_1b_2+b_1d_2\\
    c_1a_2+d_1c_2&c_1b_2+d_1d_2
    \end{matrix}
\right]
$$

### 整列相乘

矩阵A矩阵B相乘，可以用矩阵B的第j个列向量右乘矩阵A得到矩阵C的第j列：

$$
\left[
	\begin{matrix}
    a&b\\
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]
=
\left[
	\begin{matrix}
    \left[
	\begin{matrix}
    a&b\\
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2\\
    c_2
    \end{matrix}
\right]|
&
\left[
	\begin{matrix}
    a&b\\
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    b_2\\
    d_2
    \end{matrix}
\right]
    \end{matrix}
\right]
$$

而且：

$$
\left[
	\begin{matrix}
    a&b\\
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2\\
    c_2
    \end{matrix}
\right]
=
\left[
	\begin{matrix}
    a\\
    c
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2
    \end{matrix}
\right]
+
\left[
	\begin{matrix}
    b\\
    d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    c_2
    \end{matrix}
\right]
$$

### 整行相乘

矩阵A的第i个行向量左乘矩阵B得到的就是矩阵C的第i行：

$$
\left[
	\begin{matrix}
    a&b\\
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]
=
\left[
	\begin{matrix}
    \left[
	\begin{matrix}
    a&b
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]\\
--------\\
\left[
	\begin{matrix}
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]
    \end{matrix}
\right]
$$

而且：

$$
\left[
	\begin{matrix}
    a&b
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]
=
\left[
	\begin{matrix}
    \left[
	\begin{matrix}
    a
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2
    \end{matrix}
\right]
+
\left[
	\begin{matrix}
    b
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    c_2&d_2
    \end{matrix}
\right]
    \end{matrix}
\right]
$$

### 列乘以行

还可以用矩阵A的每一列左乘矩阵B的每一行，得到的每一个矩阵相加的和即为矩阵C：

$$
\left[
	\begin{matrix}
    a&b\\
    c&d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2\\
    c_2&d_2
    \end{matrix}
\right]
=
\left[
	\begin{matrix}
    a\\
    c
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    a_2&b_2
    \end{matrix}
\right]
+
\left[
	\begin{matrix}
    b\\
    d
    \end{matrix}
\right]
*
\left[
	\begin{matrix}
    c_2&d_2
    \end{matrix}
\right]
$$

### 分块相乘

矩阵的乘法方法同样适用于子矩阵，假设
矩阵A由矩阵A1，A2，A3，A4组成，矩阵B由矩阵B1，B2，B3，B4组成

则：

$$
A*B=
\left[
	\begin{matrix}
    A1B1+A2B3&A1B2+A2B4\\
    A3B1+A4B3&A3B2+A4B4
    \end{matrix}
\right]
$$

其它矩阵的乘法方式同样适用。

## 可逆矩阵

可逆矩阵又被称为非奇异矩阵，它可以**经过矩阵的初等变换化为单位矩阵**。

不可逆矩阵又被成为奇异矩阵，它的**行列式的值为0**，并且**通过初等变换可以将它化为零矩阵**。
假设A为某矩阵，如果存在非零向量x使得**Ax=0**，则A为不可逆矩阵

### 高斯-若尔当消元法

求一个矩阵的逆矩阵，我们可以使用高斯-若尔当消元法：
假设有可逆矩阵A，单位矩阵I则增广矩阵[A|I]可经由行初等变换转换为[I|A^{-1}]

示例：

$$
将矩阵
\left[
	\begin{matrix}
    1&2\\
    3&4
	\end{matrix}
\right]
转换为逆矩阵：
$$

$$
构建增广矩阵
\left[
	\begin{matrix}
    1&2&|&1&0\\
    3&4&|&0&1
	\end{matrix}
\right]
，然后进行初等行变换
$$

$$
\left[
	\begin{matrix}
    1&2&|&1&0\\
    3&4&|&0&1
	\end{matrix}
\right]\\
-->
\left[
	\begin{matrix}
    1&2&|&1&0\\
    0&-2&|&-3&1
	\end{matrix}
\right]\\
-->
\left[
	\begin{matrix}
    1&0&|&-2&1\\
    0&-2&|&-3&1
	\end{matrix}
\right]\\
-->
\left[
	\begin{matrix}
    1&0&|&-2&1\\
    0&1&|&\frac{3}{2}&-\frac{1}{2}
	\end{matrix}
\right]\\
得到逆矩阵
\left[
	\begin{matrix}
    &-2&1\\
    &\frac{3}{2}&-\frac{1}{2}
	\end{matrix}
\right]
$$

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
