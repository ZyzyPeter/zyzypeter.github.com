---
layout: post
title: 线性代数 最小二乘法与其推导
categories: linear-algebra
description: 线性回归初步
keywords: linear-algebra,machine-learning
---

## 正交与投影

### 正交的定义

对于向量$$x=[x_1,x_2,x_3,...,x_n]^T,y=[y_1,y_2,y_3,...,y_n]^T$$，当$$x^T·y=0时,即$$
$$
x_1y_1+x_2y_2+...+x_ny_n=0时，
$$
有向量$$x，y$$正交。

##### 毕达哥拉斯定理，$$x^Ty=0$$为正交的推导

毕达哥拉斯定理提到，直角三角形的三条边满足：
$$
|\vec{x}|^2+|\vec{y}|^2=|\vec{x+y}|^2\\
~~\therefore x^Tx+y^Ty=(x+y)^T(x+y)=x^Tx+y^Ty+x^Ty+y^Tx\\
\therefore 0=2x^Ty+y^Tx~~~~~~~~~~~~对于向量点乘，x^Ty=y^Tx\\
\therefore x^Ty=0
$$

### 投影矩阵

##### 用处

假设P为A的投影矩阵，则P乘以**任意向量b**，都可得到b在矩阵A的列空间上的**投影p**，即：
$$
Pb=p
$$
投影矩阵P的表达式为：
$$
P=A(A^TA)^{-1}A^T
$$

##### 推导

$$
记向量b在矩阵A的列空间上的投影为p，则可设线性方程Ax=p，设e=b-p\\
则由投影的性质可知，e与矩阵A正交，即\\
A^Te=0，即A^T(b-Ax)=0\\
\therefore A^Tb=A^TAx\\
$$

$$
所以可得x=(A^TA)^{-1}A^Tb，所以p=A(A^TA)^{-1}A^Tb\\
由A的投影矩阵P性质可得，Pb=p，所以化简得A的投影矩阵：\\
P=A(A^TA)^{-1}A^T
$$

## 最小二乘法

##### 引入问题

假设在平面直角坐标系中有三个点，分别为$$b_1(1,1),b_2(2,2),b_3(3,2)$$，需要求一条直线尽可能的经过这三个点，因为已知不可能有直线完全经过三点，该直线仅为与这三点拟合的回归直线。

要作出这条直线，我们需要先假设，
$$
b_1,b_2,b_3到直线上同一横坐标的点的纵坐标差值为e_1,e_2,e_3，\\
直线上与b_1,b_2,b_3为同一横坐标的点分别为p_1,p_2,p_3，这三个点又可称为\\
b_1,b_2,b_3在直线上的投影。
$$
作图如下：
![](\images\blog\index.png)
图片来源：[http://nbviewer.jupyter.org/github/zlotus/notes-linear-algebra/blob/master/chapter16.ipynb](http://nbviewer.jupyter.org/github/zlotus/notes-linear-algebra/blob/master/chapter16.ipynb)

求该**直线的表达式**

### 最小二乘法公式

最小二乘法用于求类似于题述中的回归直线的表达式，它有两个公式：

$$
假设直线的表达式为 y=C+Dx\\
则一般最小二乘法公式：
D=\frac{\sum^n_{i=1}x_iy_i-\sum^n_{i=1}x_i\sum^n_{i=1}y_i}{\sum^n_{i=1}x_i^2-(\sum^n_{i=1}x_i)^2}，C=\bar{y}-D*\bar{x}，(\bar{x},\bar{y}表示x和y的平均值)
$$
$$
线性代数最小二乘法公式：
A^TA
\left[
\begin{matrix}
	\hat{C}\\
    \hat{D}
\end{matrix}
\right]
=A^Tb，其中A为方程：
\left\{
\begin{matrix}
	C+Dx_1=y_1\\
    C+Dx_2=y_2\\
    ...\\
    C+Dx_n=y_n
\end{matrix}
\right.
$$
$$
的系数矩阵
\left[
\begin{matrix}
	1&x_1\\
    1&x_2\\
    ...\\
    1&x_n
\end{matrix}
\right]
，b=
\left[
\begin{matrix}
	y_1\\
    y_2\\
    ...\\
    y_n
\end{matrix}
\right]
$$

### 线性代数最小二乘法公式推导

引入题设问题，三点列方程得到
$$
\left\{
\begin{matrix}
	C+1D=1\\
    C+2D=2\\
    C+3D=2
\end{matrix}
\right.
$$
从而得出线性方程$$Ax=b$$，设三点关于回归直线的偏差为$$e_1,e_2,e_3$$，则要使偏差最小，即$$e_1^2+e_2^2+e_3^2=(p_1-y_1)^2+(p_2-y_2)^2+(p_3-y_3)^2$$这个式子的值最小，直接通过对C，D分别求偏导得到C和D的表达式，也即一般最小二乘法公式。

$$
又因为e_1,e_2,e_3分别为b_1,b_2,b_3到它们在直线上的投影点p_1,p_2,p_3的距离，\\且由线性方程Ax=b可得直线为该方程的解(准确的说应该是A\hat{x}=b的解)，
$$

$$
所以向量Ax应为向量b在矩阵A的列空间上的投影p，向量e=（b-p）为点与直线之间\\
的误差，\\
因为e与矩阵A正交，所以A^Te=0\\
\therefore A^T(b-Ax)=0\\
\therefore A^TAx=A^Tb
$$

因此可以通过
$$
A^TA=
\left[
\begin{matrix}
	3&6\\
    6&14
\end{matrix}
\right]
，
A^Tb=
\left[
\begin{matrix}
	5\\
    11
\end{matrix}
\right]\\
得到
x=
\left[
\begin{matrix}
	\hat{C}\\
    \hat{D}
\end{matrix}
\right]
=
\left[
\begin{matrix}
	\frac{2}{3}\\
    \frac{1}{2}
\end{matrix}
\right]
即回归直线y=\frac{2}{3}+\frac{1}{2}x
$$

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>