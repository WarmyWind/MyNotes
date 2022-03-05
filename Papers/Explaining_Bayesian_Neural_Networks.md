# Explaining Bayesian Neural Networks

## Introduction

由于很难解释到底是什么影响了模型的预测，DNN经常被视作**黑盒**模型。但是，决策模型的透明性是很关键的（尤其是在一些注重安全性能的应用上），否则模型就没有能力解释它所做的决策，进而限制模型的应用场景。

近年来，可解释AI（Explainable AI，XAI）领域提出了一些解释AI的方法（主要包括全局方法和局部方法），但这些方法大多是用于解释MAP准则训练下的DNN的。不同于DNN，BNN训练得到网络权重的分布，进而得到预测结果的分布。BNN不仅提供预测结果，也给出了不确定度信息。一些DNN的解释方法不能直接拿来用于解释BNN。本文提出的一种方法将BNN的不确定信息转换为（输入空间中的）特征相关不确定性，从而产生误差柱状图（或不确定热力图）进行解释。

> 可解释性的英文单词有两个：Explainability和Interpretability。这两个词是有一些区分的。Explainability是理解模型运作的数学机理；Interpretability可译为可推理性，它关注对于一个输入，我们能否根据对模型的理解，保证模型可以得到预期的输出。完整的Explainability是很难实现的，因此目前提到的可接受性大都是Interpretability。
>
> 该部分参考[https://zhuanlan.zhihu.com/p/123029741](https://zhuanlan.zhihu.com/p/123029741%E2%80%B8)

## Background and Related Work

1. 贝叶斯神经网络
   略。
2. 局部归因方法
   局部解释方法将相关性归因于输入（特征）或者中间节点，这种联系需要用到相关性运算：

   > 相关性运算的定义为：
   > 一种运算${{{\mathcal T}}_{x,W}}$将输出函数${f_W}:{\mathbb R^d} \to {\mathbb R^k}$映射到一个相关性函数上${R}:{\mathbb R^d} \to {\mathbb R^d}$，即
   >
   > $$
   > {R_W}\left( x \right) = {\mathcal T_{x,W}}\left[ {{f_W}} \right]\left( x \right)
   > $$
   >

   上述定义假定了输入的特征（或节点）的相关性主要依赖于输出函数的输入。

   在本文中，BNN的解释框架主要基于分层相关性传播（Layer-wise Relevance Propagation，LRP）。

## XAI for BNNs

目前（截至文章发布）大部分解释BNN的工作都关注不确定性量化和可视化。

## Explaining Bayesian Neural Networks

记训练集为$D_{tr}$，设权重的后验分布为$W \sim p\left( {W\left| {{D_{tr}}} \right.} \right)$，那么在给定数据$x$的情况下，原文给出的相关性分布为

$$
p\left( {R\left| {x,{D_{tr}}} \right.} \right) = \int {{R_W}\left( x \right)} p\left( {W\left| {{D_{tr}}} \right.} \right)dW
$$

> 我认为这里文章的公式写错了。上式的右边显然是计算相关性期望的式子，即
>
> $$
> \mathbb E\left[ {{R_W}\left( x \right)} \right] = \int {{R_W}\left( x \right)} p\left( {W\left| {{D_{tr}}} \right.} \right)dW
> $$
>
> 若要计算相关性分布$p\left( {R\left| {x,{D_{tr}}} \right.} \right)$，应该服从以下式子
>
> $$
> \begin{array}{c}p\left( {R\left| {x,{D_{tr}}} \right.} \right) = \int {p\left( {R,W\left| {x,{D_{tr}}} \right.} \right)} dW\\ = \int {p\left( {R\left| {W,x} \right.} \right)p\left( {W\left| {{D_{tr}}} \right.} \right)dW} \\ = \int {1\left\{ {R = {R_W}\left( x \right)} \right\}} p\left( {W\left| {{D_{tr}}} \right.} \right)dW\end{array}
> $$

### Average Explanation

有些情况下，数据相关性的均值恰好等于数据均值的相关性，这里文章给出定理1：

> 定理1：如果相关性运算${{{\mathcal T}}_{x,W}}$是线性的，且不依赖于$W$（即${{\mathcal T}_{x,W}}={{\mathcal T}_x}$），那么有
>
> $$
> {{\mathcal T}_x}\left[ {{\mathbb E_W}\left[ {{f_W}\left( x \right)} \right]} \right] = {\mathbb E_W}\left[ {{{\mathcal T}_x}\left[ {{f_W}\left( x \right)} \right]} \right] = {\mathbb E_W}\left[ {{R_W}\left( x \right)} \right]
> $$

包括LRP-0在内的许多解释方法都满足该定理。

### Exploring multi-modality of explanations

BNN可以被认为是多个预测模型的聚合（可以称为“multi-modality，多模性”），因此如果简单地使用平均解释方法，我们会损失BNN在决策过程中的内在信息。

为了研究BNN的“主要”策略，并把BNN相类似的策略进行聚类，文章使用了谱相关性分析（Spectral Relevance Analysis，SpRAy）聚类方法。

### Union and Intersection Explanation

文章考虑了一种叫UAI（Union and Intersection）的方法，定义某个像素点的相关性小于$\epsilon$的概率为

$$
{{\rm UAI}} _\alpha ^ + \left( x \right) = {F_\varepsilon }\left[ {p\left( {R\left| {x,{D_{tr}}} \right.} \right)} \right]
$$

用这个概率值绘制热力图，文章结果显示，比起直接平均，UAI能得到更多关于显著性的信息。
