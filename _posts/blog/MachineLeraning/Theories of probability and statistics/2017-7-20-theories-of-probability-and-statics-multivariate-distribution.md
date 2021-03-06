---
layout: post
title: 概率论 多维随机变量及其分布
categories: theories-of-probability-and-statistics
description: 概率论第三章
keywords: theories of probability and statistics
---

资料来源：
[概率论与数理统计公式整理 百度文库](https \://wenku.baidu.com/view/11d3577e5acfa1c7aa00cc9b.html)

## 联合分布律

### 离散型

设$$ε(x,y)$$所有可能取值为$$(x_i,y_j)(i,j=1,2...)$$，且事件$$\{ε=(x_i,y_j)\}$$的概率为$$p_{ij}$$，称：

$$
P\{(X,Y)=(x_i,y_i)\}=p_{ij}(i,j=1,2...)
$$

为ε=(X,Y)的分布律，或称为X和Y的联合分布律。联合分布律有时也用下面的概率分布表来表示：

![](\images\blog\tps \3-1.png)

这里$$p_{ij}$$具有下面两个性质：
1. $$p_{ij}≥0(i,j=1,2...)$$
2. $$\sum_i\sum_jp_{ij}=1$$

### 连续型

对于二维随机变量$$ε=(X,Y)$$如果存在非负函数$$f(x,y)(-\infty < x < +\infty ,-\infty < y < +\infty)$$ 
使对任意一个其邻边分别平行于坐标轴的矩形区域D，即

$$
D=\{(X,Y)\mid a < x < b,c < y < d\}
$$

有

![](\images\blog\tps \3-2.png)

则称ε为连续型随机变量；并称f(x,y)为ε=(X,Y)的分布密度或称为X和Y的联合分布密度。

二维联合分布密度具有下面两个性质：
1. $$f(x,y)≥0$$
2. $$\int_{-\infty}^{+\infty}\int_{-\infty}^{+\infty}f(x,y)dxdy = 1$$

### 二维随机变量的本质

![](\images\blog\tps \3-3.png)

## 联合分布函数

### 定义

设(X,Y)为二维随机变量，对于任意实数 x,y,二元函数

$$
F(x,y)=P\{X ≤ x,Y ≤ y\}
$$

称为二维随机变量(X,Y)的分布函数，或称为随机变量X和Y的联合分布函数。它是

1. 以全平面为其定义域
2. 以事件$$\{(ω_1,ω_2)\mid -\infty  < X(ω_1) ≤ x ,-\infty < Y(ω_2) ≤ y\}$$
的概率为函数值

的一个实值函数。

### 性质

1. $$0 ≤ F(x,y) ≤ 1$$
2. F(x,y)分别对x和y是递增的，即当$$x_2 > x_1时，有F(x_2,y) ≥F(x_1,y)，对y同理$$
3. F(x,y)分别对x和y是右连续的，即
$$
F(x,y)=F(x+0,y)\\F(x,y)=F(x,y+0)
$$
4. ![](\images\blog\tps \3-4.png)
5. 对于$$x_1 < x_2,y_1 < y_2$$有
![](\images\blog\tps \3-5.png)

### 离散型与连续型的关系

![](\images\blog\tps \3-6.png)

## 边缘分布

### 离散型

X的边缘分布为

![](\images\blog\tps \3-7.png)

Y的边缘分布为

![](\images\blog\tps \3-8.png)

### 连续型

X的边缘分布密度

![](\images\blog\tps \3-9.png)

Y的边缘分布密度

![](\images\blog\tps \3-10.png)

## 条件分布

### 离散型

已知$$X=x_i$$的条件下，Y取值的条件分布为

![](\images\blog\tps \3-11.png)

已知$$Y=y_j$$的条件下，X取值的条件分布

![](\images\blog\tps \3-12.png)

### 连续型

已知X=x的条件下Y的条件分布密度

![](\images\blog\tps \3-13.png)

已知Y=y，X的条件分布密度

![](\images\blog\tps \3-14.png)

## 独立性

### 一般型

$$
F(X,Y)=F_X(x)F_Y(y)
$$

### 离散型

![](\images\blog\tps \3-15.png)

有零则不独立

### 连续型

$$
f(x,y)=f_X(x)f_Y(y)
$$

充要条件：
1. 可分离变量
2. 正概率密度区间为矩形

### 二维正态分布

![](\images\blog\tps \3-16.png)

### 随机变量的函数

若$$X_1,X_2,,,X_m ,X_{m+1},,X_n$$相互独立，h,g为连续函数，则：

$$
h(X_1,X_2,,,X_m)和g(X_{m+1},,,X_n)
相互独立
$$

特例：若X与Y独立，则h(X)和g(Y)独立。

## 二位均匀分布

设随机向量（X，Y）的分布密度函数为

![](\images\blog\tps \3-17.png)

其中$$S_D$$为区域D的面积，则称（X，Y）服从D上的均匀分布，记为$$(X,Y)\sim U(D)$$

![](\images\blog\tps \3-18.png)

## 二维正态分布

设随机向量(X,Y)的分布密度函数为

![](\images\blog\tps \3-19.png)

其中![](\images\blog\tps \3-20.png)是五个参数，则称(X,Y)服从二维正态分布。

记为![](\images\blog\tps \3-21.png)

由边缘密度的计算公式，可以推出二维正态分布的两个边缘分布仍为正态分布，即

![](\images\blog\tps \3-22.png)

但两个边缘分布是正态分布不代表联合分布就是正态分布。

## 函数分布

### Z=X+Y

根据定义计算：

$$
F_Z(z)=P(Z≤z)=P(X+Y≤z)
$$

对于连续型，
$$
f_Z(z)=\int^{+\infty}_{-\infty}f(x,z-x)dx
$$

两个独立的正态分布的和仍为正态分布$$(μ_1+μ_2,σ^2_1+σ^2_2)$$

n个相互独立的正态分布的线性组合，仍服从正态分布：

![](\images\blog\tps \3-23.png)

### $$Z=max,min(X_1,X_2,,,X_n)$$

若$$X_1,...,X_n$$相互独立，其分布函数分别为$$F_{X_1}(x)...F_{X_n}(x)$$，则$$Z=max,min(X_1,X_2,,,X_n)$$的分布函数为

![](\images\blog\tps \3-24.png)


<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>