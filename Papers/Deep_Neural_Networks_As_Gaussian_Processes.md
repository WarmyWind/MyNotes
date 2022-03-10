# Deep Neural Networks As Gaussian Processes

人们很早就知道单层全连接神经网络在参数的先验是i.i.d时，在网络宽度趋于极限时等价于高斯过程（Gaussian Process，GP）。该文章得到了无限宽度**深度**神经网络与GP之间的确切等式关系。

## Introduction

深度神经网络是一种灵活的参数化模型，而GP则是一种非参数化模型。人们已经发现在网络宽度为无限时，两种方法存在对应关系。这种对应关系说明，如果我们选择无限宽度神经网络的假设空间，权重的i.i.d先验可以被对应的一个GP先验代替。

## Deep, Infinitely Wide Neural Networks Are Drawn From GPs

### Notation

考虑$L$隐藏层全连接神经网络，用$N_l,l=1,\cdots,L$表示每一层的宽度，非线性基函数为$\phi$。用$x\in{\mathbb R}^{d_{in}}$表示网络输入，$z^L\in{\mathbb R}^{d_{out}}$表示网络输出。用$x_i^l$表示经过非线性基函数后的值（用$x_i^0\equiv x_i$表示输入，并且当用希腊字母$\alpha$作为上标时，$x^\alpha$表示某个具体的输入数据），$z_i^l$表示经过仿射变换后的值。仿射变换的权重和偏置用$W_{ij}^l$和$b_i^l$表示，并且权重和偏置都是独立的随机变量经过随机采样得到。$\mathcal {GP}\left(\mu,K\right)$表示了以均值和协方差函数分别为$\mu\left(\cdot\right)$和$K\left(\cdot,\cdot\right)$。

### Review of Gaussian Processes and Single-layer Neural Networks

在单隐藏层神经网络中，满足

$$
z_j^1\left( x \right) = b_i^1 + \sum\limits_{j = 1}^{{N_1}} {W_{ij}^1x_j^1\left( x \right)}
$$

$$
x_j^1\left( x \right) = \phi \left( {b_j^0 + \sum\limits_{k = 1}^{{d_i}_n} {W_{jk}^0{x_k}} } \right)
$$

我们假设输入$x$是独立的，而且权重和偏置都是i.i.d的。这样$x_j^1,j=1,\cdots,N_1$是相互独立的，而$z_i^1$是一系列i.i.d项的求和，由中心极限定理，在$N_1\to\infin$时，$z_i^1$将服从高斯分布。

> 中心极限定理：大量相互独立的随机变量的均值经适当标准化后依分布收敛于标准正态分布。经典中心极限定理需要i.i.d条件。而林德伯格－费勒定理是中心极限定理的高级形式，它表明满足一定条件时，独立，但不同分布的随机变量序列的标准化和依然以标准正态分布为极限。详见[https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%BF%83%E6%9E%81%E9%99%90%E5%AE%9A%E7%90%86](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%BF%83%E6%9E%81%E9%99%90%E5%AE%9A%E7%90%86%E2%80%B8)

相类似地对于$k$个输入数据和相应的输出，从多维中心极限定理可知有限集$\{z_i^1\left(x^{\alpha=1}\right),\cdots,z_i^1\left(x^{\alpha=k}\right)\}$是一个联合多变量高斯分布，**这正是一个高斯过程的定义**。

因此我们可以写$z_i^1\sim\mathcal {GP}\left(\mu^1,K^1\right)$。我们假设偏置和权重都是零均值的，并且方差分别为$\sigma_b^2$和$\sigma_w^2/N_l$，这样我们可知这个GP的均值为零，协方差函数为

$$
{K^1}\left( {x,x'} \right) \equiv \mathbb E\left[ {z_i^1\left( x \right)z_i^1\left( {x'} \right)} \right] = \sigma _b^2 + \sigma _w^2\mathbb E\left[ {x_i^1\left( x \right)x_i^1\left( {x'} \right)} \right] = \sigma _b^2 + \sigma _w^2C\left( {x,x'} \right)
$$

### Gaussian Processes and Deep Neural Networks

在深度神经网络中，满足

$$
z_j^l\left( x \right) = b_i^l + \sum\limits_{j = 1}^{{N_l}} {W_{ij}^l x_j^l\left( x \right)}
$$

$$
x_j^l\left( x \right) = \phi \left( z_j^{l-1}\left(x\right) \right)
$$

设$z_i^l\sim\mathcal {GP}\left(0,K^l\right)$，协方差函数为

$$
{K^l}\left( {x,x'} \right) \equiv \mathbb E\left[ {z_i^l\left( x \right)z_i^l\left( {x'} \right)} \right] = \sigma _b^2 + \sigma _w^2{\mathbb E_{z_i^{l - 1} \sim \mathcal {GP}\left( {0,{K^{l - 1}}} \right)}}\left[ {\phi \left( {z_i^{l - 1}\left( x \right)} \right)\phi \left( {z_i^{1 - 1}\left( {x'} \right)} \right)} \right]
$$

上式中的期望项是在GP上计算的，实际上就是在$z_i^{l - 1}\left(x\right)$和$z_i^{l - 1}\left(x'\right)$的二维联合高斯分布上计算，而这个分布是零均值，协方差阵由${K^{l-1}}\left( {x,x'} \right)$、${K^{l-1}}\left( {x,x} \right)$和${K^{l-1}}\left( {x',x'} \right)$这三项控制。因此我们可以将期望项用一个函数来表示：

$$
{\mathbb E_{z_i^{l - 1} \sim \mathcal {GP}\left( {0,{K^{l - 1}}} \right)}}\left[ {\phi \left( {z_i^{l - 1}\left( x \right)} \right)\phi \left( {z_i^{1 - 1}\left( {x'} \right)} \right)} \right]\equiv F_\phi\left({K^{l-1}}\left( {x,x'} \right),{K^{l-1}}\left( {x,x} \right),{K^{l-1}}\left( {x',x'} \right) \right)
$$

并且函数$F_\phi$的具体形式仅和基函数（激活函数）$\phi$有关。
