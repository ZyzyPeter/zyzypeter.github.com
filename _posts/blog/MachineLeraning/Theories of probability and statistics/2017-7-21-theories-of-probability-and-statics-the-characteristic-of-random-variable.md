---
layout: post
title: 概率论 随机变量的数字特征
categories: theories-of-probability-and-statistics
description: 概率论第四章
keywords: theories of probability and statistics
---

资料来源：
[概率论与数理统计公式整理 百度文库](https \://wenku.baidu.com/view/11d3577e5acfa1c7aa00cc9b.html)

## 一维随机变量的数字特征

### 期望

定义：期望就是加权平均值

符号：E(X)，表示以X为随机变量的期望

#### 离散型

X是离散型随机变量，其分布律为

$$
P(X=x_k)=p_k,k=1,2,,,n
$$

则

$$
E(X)=\sum^n_{k=1}p_kx_k
$$

（要求绝对收敛）

#### 连续型

设X是连续型随机变量，其概率密度为f(x),

$$
E(X)=\int^{+\infty}_{-\infty}xf(x)dx
$$

（要求绝对收敛）

### 函数的期望

#### 离散型

若Y=g(X)

$$
E(Y)=\sum^n_{k=1}g(x_k)p_k
$$

#### 连续型

若Y=g(X)

$$
E(Y)=\int^{+\infty}_{-\infty}g(x)f(x)dx
$$

### 期望的性质

![](\images\blog\tps\4-9.png)

### 方差与标准差

#### 方差

符号：D(X)

公式：

$$
D(X)=E[X-E(X)]^2
$$

#### 标准差

符号：σ(X)

公式：

$$
σ(X)=\sqrt{D(X)}
$$

#### 离散型

![](\images\blog\tps\4-1.png)

#### 连续型

![](\images\blog\tps\4-2.png)

#### 方差的性质

(1) $$D(C)=0;E(C)=C$$
(2) $$D(aX)=a^2D(X)\\E(aX)=aE(X)$$
(3) $$D(aX+b)=a^2D(X)$$
(4) $$D(X)=E(X^2)-E^2(X)$$
(5) $$D(X±Y)=D(X)+D(Y),\\充分条件：X和Y独立\\充要条件：X和Y不相关$$
(6)$$E(\sum^n_{i=1}S_i)=\sum^n_{i=1}E(X_i)\\D(\sum^n_{i=1}X_i)=$$
$$
\sum^n_{i=1}D(X_i)+2\sum^n_{i=1,j > i}E\{ [X_i-E(X_i)] [X_j-E(X_j)] \}
$$

### 矩

#### 离散型

1. 对于正整数k，称随机变量X的k次幂的数学期望为X的k阶原点矩，记为$$v_k$$，即
![](\images\blog\tps\4-3.png)
2. 对于正整数k，称随机变量与E(X)的差的k次幂的数学期望为X的k阶中心矩，记为$$μ_k$$，即
![](\images\blog\tps\4-4.png)

#### 连续型

1. 对于正整数k，称随机变量X的k次幂的数学期望为X的k阶原点矩，记为$$v_k$$，即
![](\images\blog\tps\4-5.png)
2. 对于正整数k，称随机变量与E(X)的差的k次幂的数学期望为X的k阶中心矩，记为$$μ_k$$，即
![](\images\blog\tps\4-6.png)

### 切比雪夫不等式

设随机变量X具有数学期望E（X）=μ，方差$$D(X)=σ^2$$，则对于任意整数ε，有下列切比雪夫不等式

![](\images\blog\tps\4-7.png)

切比雪夫不等式给出了在未知X的分布的情况下，对概率

![](\images\blog\tps\4-8.png)

的一种估计，它在理论上有重要意义。

### 常见分布的期望和方差

![](\images\blog\tps\4-11.png)

![](\images\blog\tps\4-12.png)

## 二维随机变量的数字特征

### 期望

#### 离散型

![](\images\blog\tps\4-13.png)

#### 连续型

![](\images\blog\tps\4-13.png)

### 函数的期望

#### 离散型

![](\images\blog\tps\4-15.png)

#### 连续型

![](\images\blog\tps\4-16.png)

### 方差

#### 离散型

![](\images\blog\tps\4-17.png)

#### 连续型

![](\images\blog\tps\4-18.png)

### 协方差

符号：$$σ_{XY}$$或$$cov(X,Y)$$

公式：

![](\images\blog\tps\4-19.png)

与其符号对应，X与Y的方差也可以记为$$σ_XX$$或者$$σ_YY$$

#### 协方差的性质

![](\images\blog\tps\4-23.png)

#### 协方差矩阵

![](\images\blog\tps\4-24.png)

### 相关系数

对于随机变量X与Y，如果D(X) > 0,D(Y) > 0 则称

![](\images\blog\tps\4-20.png)

为X与Y的相关系数，记作$$ρ_{XY}$$（有时可简记为ρ）

性质：
1. $$\mid ρ \mid ≤1$$
2. 当$$\mid ρ \mid =1$$时，称X与Y完全相关：$$P(X=aY+b)=1$$
![](\images\blog\tps\4-21.png)
3. 当ρ=0时称X与Y不相关。

以下五个命题是等价的：

![](\images\blog\tps\4-22.png)

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>