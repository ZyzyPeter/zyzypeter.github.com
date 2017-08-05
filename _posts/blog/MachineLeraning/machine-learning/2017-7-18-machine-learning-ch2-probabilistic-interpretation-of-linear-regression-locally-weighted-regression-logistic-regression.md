---
layout: post
title: 机器学习 第二章 机器学习 线性回归的概率解释 局部加权回归 逻辑回归
categories: machine-learning
description: 机器学习
keywords: linear-algebra, Theories of probability and statistics, machine-learning
---

资料来源：

[CS99 Machine Learning Course Materials Lecture notes1](http://cs229.stanford.edu/notes/cs229-notes1.pdf)

[线性回归的概率解释、局部加权回归、逻辑回归](http://nbviewer.jupyter.org/github/zlotus/notes-LSJU-machine-learning/blob/master/chapter03.ipynb)

## 线性回归的概率解释

现在我们来讨论一下，为什么，线性回归，特别是最小二乘法是一个合理的选择，而不使用误差的绝对值亦或是四次方去推导线性函数。

### 似然函数

我们先假设输出值和输入值有下面这个方程关系：

$$
y^{(i)}=θ^Tx^{(i)}+ε^{(i)}
$$

其中ε<sup>(i)</sup>是**误差项**，它捕获了可能的未建模影响因素。也可能包含了存在于样本中的随机噪音。我们进一步假设ε<sup>(i)</sup>之间是相互独立的（IID，independently and identically distributed），那么，它就会服从**高斯分布**（正态分布）的形式，表示为

$$
ε^{(i)}\sim N(0,σ^2)\\
$$

因此ε<sup>(i)</sup>的概率密度函数为：

![](\images\blog\ml2-1.png)

由此我们可以推出

![](\images\blog\ml2-2.png)

这个公式的转换可以认为是从数学的角度上，将第一条公式中的$$ε^{(i)}$$直接用$$y^{(i)}，θ^Tx^{(i)}$$和$$ε^{(i)}$$的方程关系进行代换。我们可以认为，这个方程的实际意义就是在输入（X）与参数（θ）确定的情况下，通过模型得出的Y与实际的Y相同的概率。

这里使用高斯分布有两个原因：
* 第一个是为了便于进行数学计算
* 第二个是因为当我们使用线性回归进行实际测试之后再回代结果以测量误差，会发现在大多数情况下误差都会服从高斯分布。当一个事件的发生是由许多随机并且相互独立的事件共同导致的，那么这个事件发生的概率就可以看作是服从高斯分布。

另外，$$p(y^{(i)}|x^{(i)};θ)$$表示对于给定$$x^{(i)}$$下$$Y$$的条件概率。我们不能将θ也纳入条件（即不能记为$$p(y^{(i)}|x^{(i)},θ)$$），因为我们确信，θ是一个已经确定的值，只是我们还并没有找到（这是频率学派的观点，而贝叶斯学派认为，θ也是随机变量）
我们还可以将这个式子表示为：

$$
y^{(i)} | x^{(i)};θ \sim N(θ^Tx^{(i)},σ^2)
$$

已知X（包含所有训练样本$$x^{(i)}$$的设计矩阵）和θ的情况下，我们应该如何求$$y^{(i)}$$的分布？这个数据的概率将由$$p(y^{(i)} \mid x^{(i)};θ)$$给出。这个函数通常被视为一个关于$$\vec{y}$$（也可能关于X）的函数，但我们现在想明确地将其视作一个关于θ的函数，因此，这时我们就会称它为它作**似然函数**(likelihood function)：

$$
L(θ)=L(θ;X,\vec{y})=p(\vec{y}|X;θ)
$$

可以看到，这里将其表示为一个关于θ的函数（我们可以认为，θ的似然性（即似然函数的值）与数据Y的概率是一样的，并且似然性和概率几乎可以说是有一样的意义，但我们也会用似然性这个词来强调这个函数是关于θ的）。所以，在以后当我们在使用“参数的似然性”和“数据的概率”时，尽管他们大多数情况下表示的值的内容是一样的，但是他们强调的对象确是不同的。

因为$$ε^{(i)}$$是独立的，（也因为$$y^{(i)}$$是由$$x^{(i)}$$给出来的），这个方程可以被扩展为：

![](\images\blog\ml2-3.png)

现在我们得到了$$x^{(i)}$$与$$y^{(i)}$$相互联系的概率模型，接下来我们应该如何推断θ的值？

为了能够使数据Y的概率尽可能的大（也即模型预测出的Y尽可能的准确）即L(θ)的值尽可能的大，我们就需要求出一个能够使得L(θ)最大的参数θ。

直接根据这个函数求θ的极值不方便运算，因此，我们还可以选择最大化任意关于L(θ)的严格增函数。特别是在要对式子求导的情况下，我们选择让**对数似然函数**取得最大值会比较简单：

![](\images\blog\ml2-4.png)

（其实这里我更偏向于是写作$$log_e$$或者$$ln$$，讲义中的数学习惯显然与我们有些不同）

因此，为了使l(θ)取得最大值，我们可以等价地认为是要让下面这个式子取最小值：

![](\images\blog\ml2-5.png)

我们可以将这个式子认为是J(θ)，也就是我们之前的最小二乘法中所用到的成本函数（cost function）。

综上，我们可以看到，在关于训练数据的概率假设中，最小二乘回归与θ的最大似然估计所用的方法是一致的，也正因此，我们会强调最小二乘法是一个相当“自然”的方法。

但是，这段概率假设并不是证明“最小二乘法是一种非常易用的，合理的方法”的必要条件，实际上他只是一个证明方法，其他的自然假设也一样可以用来证明。

并且，在我们最后的讨论中可以发现，σ<sup>2</sup>的取值并不影响θ的求解，事实上哪怕σ是一个未知的值我们也可以得到同样的结果。

## 局部加权回归

### 欠拟合与过拟合

![](\images\blog\ml2-6.png)

首先让我们先观察一下这幅图片，假设我们有一组通过一维输入数组X确定的Y，并且把他们标在图上。我们可以看到，图中的三条曲线，就是关于训练集的三条拟合曲线。显然，最左边回归方程为$$y=θ_0+θ_1x$$的直线的拟合效果并不强。这种拟合效果欠佳的情况，我们称之为**欠拟合**

接着，我们加上一个$$x^2$$，回归方程变为$$y=θ_0+θ_1x+θ_2x^2$$（即中间那幅图），可以发现，拟合效果似乎随着特征的增加而变强了。

但事实上，通过第三条曲线（他的回归方程为$$y=\sum^5_{j=0}θ_jx^j$$），我们可以看到，当特征的数量过多的时候，曲线会近乎完美的去拟合每一个样本，但也同时导致曲线的走势变化过多，所以我们也不会认为这条曲线能够对y做出一个好的预测。而这种情况，我们就称之为**过拟合**

### 局部加权

现在让我们来讨论一下局部加权回归(LMR,locally weighted linear regression)。

假设我们有非常充足的训练数据，使得我们对于特征的提取要求变得不那么严格。
在前面的原始的线性回归中，我们可以看到，为了对x（或者说h(x)的值）做出预测，我们需要做出以下几步：

1. 调整θ使得$$\sum_i(y^{(i)}-θ^Tx^{(i)})^2$$最小
2. 输出$$θ^Tx$$

与之相比，局部加权回归算法则会做下面这几步：

1. 调整θ使得$$\sum_iω^{(i)}(y^{(i)}-θ^Tx^{(i)})^2$$最小
2. 输出$$θ^Tx$$

在这里的这些ω<sup>(i)</sup>是一些非负**权值**。直观上看，如果对于某个样本,ω<sup>(i)</sup>的取值过大，则在计算时我们就要尽可能的缩小$$(y^{(i)}-θ^Tx^{(i)})^2$$项的取值（精确拟合）；反之，如果ω<sup>(i)</sup>取值较小，计算$$(y^{(i)}-θ^Tx^{(i)})^2$$项得到的误差项就会足够小以至于减少这个样本对整个误差项的影响。

**指数衰减函数**（exponential decay function）是一种比较好的权值设置方式（所以当然也有其它的用于设置权值的方式）：

![](\images\blog\ml2-7.png)

容易看出，当我们已知查询点x（注意不是x<sup>(i)</sup>）时，权值的大小与训练样本x<sup>(i)</sup>相对于x的位置（即两者之间的空间距离）密切相关：

1. 如果x<sup>(i)</sup>距离x很**近**，那么ω<sup>(i)</sup>就会取**靠近1**的值
2. 如果x<sup>(i)</sup>距离x很**远**，那么ω<sup>(i)</sup>就会取**靠近0**的值

因此，通过这种方法得到的θ将会更注重查询点x附近的训练样本（权值较高）的精确拟合，而距离较远的训练样本（权重较低）就会被忽略。（尽管权值函数看起来很像高斯函数，但它与高斯分布没有多大的关系。并且，ω<sup>(i)</sup>并不是随机变量，它也不服从高斯分布，只是恰好也是一个类似于钟形的曲线）

函数中的τ成为**带宽**(bandwidth)（或波长）参数，它控制了权值随距离下降的速率：
* 如果τ值较**小**，就会得到一个较**窄**的钟形曲线，这意味着随着离给定查询点x的距离越大，训练样本x<sup>(i)</sup>的权值下降的速度就越**快**
* 如果τ值较**大**，就会得到一个较**宽**的钟形曲线，这意味着随着离给定查询点x的距离变大时，训练样本x<sup>(i)</sup>的权值下降的速度会相对较**慢**

需要注意的是，这是一个**非参数算法**。所以我们在使用这个方法时，是针对给定的查询点x进行计算，也就是说，每当我们给定一个x时，都需要根据整个训练集做出拟合运算，如果训练集很大而且查询非常频繁的话，这个算法的代价也会变得非常高。

## 分类问题和逻辑回归

### 二元分类

现在我们来讨论一下分类问题，它和逻辑回归问题很相似，但是它的y值（也即是说我们打算预测的值）只是少量的一些离散值。

这一次我们先介绍**二元分类**(binary classification)，也就是说，y值只能够取0或者1。（二元分类中介绍的一些方法也可以推广至多元分类）

让我们先举个例子：

假设，我们试图实现一个垃圾邮件分类器，那么x<sup>(i)</sup>就是邮件的特征，y值1表示这个样本是一封垃圾邮件，0表示不是。在这里，y的0值又被称作**negative class**（负类？），1被称作**positive class**（正类？），有时候也会用符号“+”和“-”代替他们。

对于给定的x<sup>(i)</sup>,y<sup>(i)</sup>也称作训练样本的**label**（标签）。

### 逻辑回归

对于分类问题，我们可以试着无视y是取离散值的特征从而继续使用线性回归，但是线性回归得到的结果往往都不会理想。并且，我们在已经明确知道$$y∈\{0,1\}$$的情况下还试图使用线性回归的$$h_θ(x)$$是不合理的。

所以，让我们来对原来的线性回归的假设(hypothesis)函数做出一些修改:

$$
h_θ(x)=g(θ^Tx)=\frac{1}{1+e^{-θ^Tx}}
$$

这里的$$g(z)=\frac{1}{1+e^{-z}}$$也被叫做**逻辑函数**（logistic function）或**S型函数**(sigmoid function)，它的图像如下：

![](\images\blog\ml2-8.png)

我们注意到g(z)在$$z \rightarrow \infty$$的时候趋向于1，在$$z \rightarrow -\infty$$的时候趋向于0.而且除了g(z)，上面的函数h(x)也是在（0，1）之间取值。

像之前一样，我们让$$x_0=1$$，这样就可以得到

$$
θ^Tx=θ_0+\sum^n_{j=1}θ_jx_j
$$

其它的在$$(-\infty,+\infty)$$上能够从0取到1的可导的连续函数也可以使用这个函数，但是因为某些在以后才会讲到的原因（讲到关于GLM和生成学习法的时候），我们选择上面的逻辑函数可以说是一种非常“自然”的结果。

再继续我们的推算之前，我们先来看一下逻辑函数求导的优良性质：

![](\images\blog\ml2-9.png)

接下来，我们应该如何用θ拟合逻辑回归模型呢？根据我们对线性回归应用的在一系列假设的情况下求极大似然的方式，我们可以赋予我们的分类模型一系列概率假设，然后通过最大似然来估计拟合参数。假设：

![](\images\blog\ml2-10.png)

当然，这个两个式子可以合为：

![](\images\blog\ml2-12.png)

假设训练集中的m个训练样本是相互独立的，我们可以这样写出参数的似然估计：

![](\images\blog\ml2-13.png)

同线性回归中的运算方式一样，我们取对数似然函数：

![](\images\blog\ml2-14.png)

为了让l(θ)取到最大值，我们这次使用**梯度上升法**，它与梯度下降一样，只不过方向θ要增大，因此，θ的梯度更新方程为：

$$
θ:=θ+α\nabla_θl(θ)
$$

这个方程中我们使用了加号（线性回归中的梯度下降我们用的是负号），因为我们想要求的是函数的最大值。（这也是它被称为梯度上升的原因）

假设训练集中只有一个训练样本(x,y)，我们对l(θ)求导以此来获得随机梯度上升的规则：

![](\images\blog\ml2-15.png)

这里我将会写出从头到尾的推导公式：

![](\images\blog\ml2-16.jpg)

然后，我们将得出的结果应用到随机梯度上升的θ更新公式：

![](\images\blog\ml2-17.png)

若是应用到批梯度上升则是：

![](\images\blog\ml2-18.png)

将他们和之前的最小均方法里面的梯度下降的θ的更新公式作比较，会发现二者一模一样。**但是**，事实上这里的$$h_θ(x)$$已经变为关于$$θ^Tx^{(i)}$$的非线性函数（它的函数式和线性回归地h(x)是不一样的），所以这与我们的线性回归并不是同一个算法。

尽管如此，对于不同的算法能过够得到相同形式的更新规则，我们还是会感到神奇。这在之后的GLM模型的课程中会给出解释。

## 感知算法

最后我们来顺便介绍一下感知算法，以后在介绍学习理论的时候我们还会再详细地讨论这个算法，这里只是简单地介绍一下。

对于上面所说的逻辑回归，我们应该怎么做才能“强迫”算法只输出0或者1？

自然地，我们会想到改变g的定义，使其变成一个阈函数：

![](\images\blog\ml2-19.png)

如果我们依然令$$h_θ(x)=g(θ^Tx)$$，并且将g修改为上面地这个阈函数，然后使用

![](\images\blog\ml2-20.png)

为θ的更新规则，那么，我们就实现了一个**感知算法**（perceptron learning algorithm）。

在六十年代，这个感知算法被认为是一种粗略地对脑中独立神经元工作方式的模型的描述。该模型很简单，所以这也将成为我们日后讨论学习理论的起点。

需要注意的是，尽管这个算法看起来并不特殊，但它又是一个与最小二乘亦或是逻辑回归完全不同的算法：比如，我们很难赋予它一个在概率上有意义的解释，也很难从最大似然估计推导出感知算法。

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>