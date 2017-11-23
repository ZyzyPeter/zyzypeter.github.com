---
layout: post
title: 机器学习 第十一章 支持向量机（4） 序列最小最优化
categories: machine-learning
description: 机器学习
keywords: linear-algebra, Theories of probability and statistics, machine-learning
---

资料参考来源：

清华大学出版社 李航 《统计学习方法》 第七章 支持向量机

## SMO的基本思路

我们在这一章将讨论的是一个高效实现支持向量机学习的算法**序列最小最优化**（sequential minimal optimization,SMO）算法。

假设我们有如下凸二次规划的对偶问题：

![](\images\blog\ml11-1.png)

在这个问题中，变量是拉格朗日乘子，一个变量$$α_i$$对应一个样本点$$(x_i,y_i)$$；变量的总数等于训练样本容量N。

SMO算法是一种启发式算法（*指一个基于直观或经验构造的算法，在可接受的花费（计算时间和空间）下给出待解决组合优化问题每一个实例的一个可行解，该可行解与最优解的偏离程度一般无法被预计*，来源：[启发式算法](https://baike.baidu.com/item/%E5%90%AF%E5%8F%91%E5%BC%8F%E7%AE%97%E6%B3%95/938987?fr=aladdin#1)），它的基本思路是：先随机选择一组变量值，如果所有变量的解都满足此优化问题的KKT条件（Karush-Kuhn-Tucker conditions），那么这个最优化问题的解就得到了。因为KKT条件是该最优化问题的充分必要条件。

如果有变量不满足，我们就选择两个变量并固定其它变量（选择两个而不是仅选择一个变量进行优化是因为约束条件$$\sum^N_{i=1}α_iy_i=0$$使得若有$$N-1$$个变量被确定，那么剩下的一个变量也会确定），仅针对这两个变量构建一个二次规划问题。该二次规划问题关于这两个变量的解会更接近原始二次规划问题的解，因为这个解得出的两个变量可以让让原始二次规划问题的目标函数值变得更小。更重要的是，这时的子问题可以通过解析方法（即用代数解决非代数问题）求解，从而大大提高整个算法的精确度。

子问题只有两个变量，一个是违反KKT条件最严重的变量（因为这些违反KKT条件的变量是我们最需要修正的），另一个由约束条件$$\sum^N_{i=1}α_iy_i=0$$自动确定：

假设$$α_1,α_2$$为两个变量，$$α_3,...,α_N$$固定，那么由约束条件可得：

![](\images\blog\ml11-2.png)

因此，我们在子问题中可以同时更新两个变量。

由此我们得到，整个SMO算法包括两个部分：
1. 求解两个变量二次规划的解析方法
2. 选择变量的启发式方法

## SMO结构示意图

![](\images\blog\mla5-4.png)

## 两个变量二次规划的求解方法

首先假设我们选择的两个变量是$$α_1,α_2$$，其它变量$$α_i(i=3,...,N)$$是固定的（即已知标量），于是我们可以将原凸二次规划的对偶问题的子问题写成：

![](\images\blog\ml11-3.png)

![](\images\blog\ml11-4.png)

其中$$K_{ij}=K(x_i,x_j),i,j=1,...,N$$，$$ζ$$是一个常数，目标函数式（7.101）中省略了不包含$$α_1,α_2$$的常数项。

要求解这个问题，我们首先要分析约束条件，然后在这个约束条件下求极小。

由于只有两个变量，我们可以建立二维平面上的坐标系来直观地看到约束情况：

![](\images\blog\ml11-5.png)

此处资料来源：[Sin的专栏 SMO算法介绍](http://blog.csdn.net/yclzh0522/article/details/6900707)

这幅图是$$y_1和y_2$$异号的情况，图中有两条斜线，上面那条表示$$\zeta$$是负数，下面那条表示$$\zeta$$是正数。从图中可以看到，$$α_1$$和$$α_2$$必须同时在矩形内和其中一条斜线上取值，例如假设$$\zeta$$是正数，则变量在图中黑色阴影部分取值。

![](\images\blog\ml11-5.jpg)

（如果$$y_1和y_2$$同号，则图片中直线的斜率为当前斜率的相反数，因为此时$$α_1+α_2=\zeta$$）

现在我们假设要考虑变量$$α_2$$的优化问题，假设这两个变量的二次规划问题的初始可行解为$$α_1^{old},α_2^{old}$$，最优解为$$α_1^{new},α_2^{new}$$，并且假设在沿着约束方向未经剪辑（未经剪辑就是说不考虑条件$$0≤α_i≤C，i=1,2$$）时$$α_2$$的最优解为$$α_2^{new,unc}$$。

因为$$α_2^{new}$$需满足不等式约束$$0≤α_i≤C$$，所以我们可以得到最优值$$α_2^{new}$$的取值范围：

![](\images\blog\ml11-6.png)

L与H分别是$$α_2^{new}$$所在的对角线段端点的界。像下图一样，

![](\images\blog\ml11-8.png)

如果$$y_1≠y_2$$则有

![](\images\blog\ml11-7.png)

若$$y_1=y_2$$像下图一样：

![](\images\blog\ml11-9.png)

则有：

![](\images\blog\ml11-10.png)

实际上我们可以很容易看出来，这两个式子是表明$$α_2^{new}$$的取值范围是由$$\zeta$$的正负决定的。

下面，我们首先沿着约束方向，即不考虑约束条件$$0≤α_i≤C，i=1,2$$的情况下求$$α_2$$的最优解$$α_2^{new,unc}$$。

为了叙述简单，记目标函数

![](\images\blog\ml11-11.png)

并令

![](\images\blog\ml11-12.png)

当$$i=1,2$$时，$$E_i$$为函数$$g(x)$$对输入样本$$x_i$$的预测值与真实输出$$y_i$$的差。

规定这两个函数之后，我们有如下定理：

### 求解两个变量最优化问题的最优解定理

两个变量的最优化问题沿着约束方向未经剪辑的解为

![](\images\blog\ml11-13.png)

![](\images\blog\ml11-14.png)

其中$$Φ(x)$$是输入空间到特征空间的映射，$$E_i,i=1,2$$，上式由$$E_i$$的表达式得到。

经剪辑后$$α_2$$的解是

![](\images\blog\ml11-15.png)

由$$α^{new}_2$$求得$$α^{new}_1$$为

![](\images\blog\ml11-16.png)

下面给出证明

### 证明

首先引进记号：

![](\images\blog\ml11-17.png)

于是，目标函数可以写成：

![](\images\blog\ml11-18.png)

由$$α_1y_1=\zeta - α_2y_2$$以及$$y_i^2=1$$，可以将$$α_1$$表示为（记得$$\zeta = -\sum^N_{i=3}y_iα_i$$）：

![](\images\blog\ml11-19.png)

把这个式子代入到上面$$W(α_1,α_2)$$的表达式中，得到仅由$$α_2$$作参数的函数的目标函数：

![](\images\blog\ml11-20.png)

对$$α_2$$求导：

![](\images\blog\ml11-21.png)

令导数为0，得到

![](\images\blog\ml11-22.png)

将$$\zeta =α_1^{old}y_1+α_2^{old}y_2$$代入式中，得到

![](\images\blog\ml11-23.png)

再将$$η=K_{11}+K_{22}-2K_{12}$$代入，最终得到

![](\images\blog\ml11-24.png)

若要使$$α_2^{new,unc}$$满足不等式约束则必须将其限制在区间$$[ L,H ]$$内，于是我们得到$$α_2^{new}$$的表达式：

![](\images\blog\ml11-15.png)

再由等式约束$$α_1y_1+α_2y_2=-\sum^N_{i=3}y_iα_i=\zeta$$得到$$α_1^{new}$$的表达式：

![](\images\blog\ml11-19.png)

从而得到两个变量最优化问题的解$$(α_1^{new},α_2^{new})$$

## 变量的选择方法

SMO算法在每个子问题中选择两个变量优化，其中至少一个变量是违反KKT条件的（因为两个没有违反KKT条件的变量本身就是这两个变量组成的二次规划问题的最优解）。

### 1.第一个变量的选择

SMO通过一个外层循环检索出训练样本中违反KKT条件最为严重的样本点，并将其对应的变量（$$α_i$$）作为第一个变量。

检验样本点$$(x_i,y_i)$$是否满足KKT条件：

![](\images\blog\ml11-25.png)

![](\images\blog\ml11-26.png)

其中$$g(x_i)=\sum^N_{j=1}α_jy_jK(x_i.x_j)+b$$

（意思就是说按双向箭头左边设定$$α_i$$的值，然后计算$$y_i(x_i)$$的值，看是否与对应的范围值（$$≥1,=1,≤1$$）匹配）

该检验在$$ε$$（预设精度）范围内进行，在检验过程中 外层循环将首先遍历所有满足条件$$0 < α_i < C$$的样本点，即在间隔边界上的支持向量。如果支持向量点都满足KKT条件，就遍历整个训练集，检验它们是否都满足KKT条件。

### 第二个变量的选择

SMO的第二个变量的选择通过内层循环实现，即第一个变量$$α_1$$已经被找到的情况下寻找$$α_2$$，寻找的$$α_2$$的条件是希望能使$$α_2$$有足够大的变化。

由式子

![](\images\blog\ml11-24.png)

和

![](\images\blog\ml11-15.png)

我们知道，$$α_2^{new}$$和$$\mid E_1-E_2\mid$$有很大的关系，为了加快计算速度，一种比较简单的做法是，选择一个能让$$\mid E_1-E_2\mid$$最大的$$α_2$$（如其所述，这个选择方式的唯一意义就是为了加快计算速度）。因为$$α_1$$已经定下来，所以$$E_1$$也是确定的，因此如果$$E_1$$是正的，我们只要选择能使$$E_2$$最小的$$α_2$$；反之如果$$E_1$$是负的，就选一个最大的$$E_2$$，为了节省时间，我们可以将所有的$$E_i$$的值都保存在一个列表中。

在特殊情况下，上述内层循环的选择方式选出的$$α_2$$有可能无法使目标函数有足够的下降（就是让目标函数值变的足够小，我们的目标就是让目标函数值变小），此时我们就可以采用下面的启发式规则来选择$$α_2$$：

遍历在间隔边界上的支持向量点，依次将其对应的变量作为$$α_2$$试用，直到目标函数有足够的下降。如果找不到合适的$$α_2$$，那么就遍历整个训练数据集；若仍然找不到合适的$$α_2$$，那么我们就放弃第一个变量$$α_1$$，通过外层循环找下一个$$α_1$$。

### 计算阈值b和差值$$E_1$$

每次当我们完成两个变量的优化之后，都要重新计算阈值b（为了更新我们的输出函数然后让新的输出函数参与下两个变量的优化）。

当$$0 < α_1^{new} < C$$时，由KKT条件得到：

![](\images\blog\ml11-27.png)

因此我们可以得到$$b_1$$的更新式：

![](\images\blog\ml11-28.png)

又因为由E的定义式我们可以得到：

![](\images\blog\ml11-29.png)

由此我们得到$$b_1^{new}$$表达式的前两项可以展开成：

![](\images\blog\ml11-30.png)

将这个式子代回$$b_1^{new}$$表达式可以得到：

![](\images\blog\ml11-31.png)

同理，如果$$0 < α_2^{new} < C$$，那么同样可以得到：

![](\images\blog\ml11-32.png)

如果$$α_1^{new}，α_2^{new}$$同时满足$$0 < α_i^{new} < C,i=1,2$$，那么必然有$$b_1^{new}=b_2^{new}$$（因为这说明两个变量对应的样本点都是支持向量，这个时候支持向量机的位置就在两者之间，是不会有疑问的）。如果$$α_1^{new}，α_2^{new}$$是0或者C，那么$$b_1^{new}$$和$$b_2^{new}$$以及它们之间的数都符合KKT条件的阈值，（因为此时两个变量对应的样本点在间隔边界之外（为0）或者是在间隔边界之内（为C）），这个时候$$b_1^{new}$$和$$b_2^{new}$$以及它们之间的数都是符合KKT条件的阈值，一般在这种时候我们选择它们的中点作为阈值$$b^{new}$$。

每一次当我们完成了两个变量的优化之后，还必须更新对应的$$E_i$$值并将它们保存在列表中。$$E_i$$值的更新需要用到$$b^{new}$$值与所有的支持向量对应的$$α_j$$：

![](\images\blog\ml11-33.png)

其中S是所有支持向量$$x_j$$的集合。

## SMO算法

最后老样子，让我们来定义一下SMO的算法。

**SMO算法**

输入：训练集T（训练集合，不再展开赘述），精度ε（标量）

输出：近似解$$\hat{α}$$

步骤：

1.取初值$$α^{(0)}=0$$（将全部α初始化为0），令$$k=0$$（这是个计数器）
2.选取优化变量$$α_1^{(k)},α_2^{(k)}$$，解析求解两个变量的最优化问题，求得最优解$$α_1^{(k+1)},α_2^{(k+1)}$$，更新$$α$$为$$α^{(k+1)}$$
3.若在精度ε范围内或满足停机条件（原文没有“或”字，此处为个人理解）：

![](\images\blog\ml11-34.png)

其中

![](\images\blog\ml11-35.png)

并转到步骤4；否则令k=k+1并转回步骤2

4.取$$\hat{\alpha}=\alpha^{k+1}$$

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>