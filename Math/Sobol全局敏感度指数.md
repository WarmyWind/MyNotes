# Sobol全局敏感度指数

本文主要参考I.M. Sobol写的《Global sensitivity indices for nonlinear mathematical models and their Monte Carlo estimates》一文。

## 介绍

假设模型可以通过$u=f\left(x\right)$表示，$x=\left(x_1,\cdots x_n\right)$。对输入$x^*$进行分析，那么对于第$k$维的**局部敏感度**为

$$
{\left( {{{\partial u} \mathord{\left/
 {\vphantom {{\partial u} {\partial {x_k}}}} \right.
 } {\partial {x_k}}}} \right)_{x = {x^*}}}
$$

与局部敏感度不同，全局敏感度不关注具体的输入，而是考虑模型$f$对输入数据的敏感度。

在下面的表述中，$I$代表单位区间$\left[0,1\right]$，$I^n$代表$n$维单位超立方体，并且$x \in I^n$。所有的积分号的积分区间都代表对每个变量在$I$上积分。

## ANOVA-representation

对不同维度的输入组合，我们都可以定义一种模型。如：只有$x_1$作为模型输入，我们可以定义模型为$f_1\left(x_1\right)$；$x_1,x_2$作为输入，定义模型为$f_{12}\left(x_1,x_2\right)$。

下面我们定义可积函数$f\left(x\right)$：

$$
\begin{aligned}
f\left( x \right) 
&= {f_0} + \sum\limits_{s = 1}^n {\sum\limits_{{i_1} <  \cdots  < {i_s}}^n {{f_{{i_1} \cdots {i_s}}}\left( {{x_{{i_1}}}, \cdots ,{x_{{i_s}}}} \right)} }  \\
&={f_0} + \sum\limits_i {{f_i}\left( {{x_i}} \right)}  + \sum\limits_{i < j} {{f_{ij}}\left( {{x_i},{x_j}} \right)}  +  \cdots  + {f_{12 \cdots n}}\left( {{x_1}, \cdots ,{x_n}} \right)
\end{aligned}
$$

当

$$
\int_0^1 {{f_{{i_1} \cdots {i_s}}}\left( {{x_{{i_1}}}, \cdots ,{x_{{i_s}}}} \right)d{x_k}}  = 0,\quad \forall k = {i_1}, \cdots ,{i_s}
$$

成立，则$f\left(x\right)$被称为ANOVA-representation。此时有诸如下面的式子成立：

$$
\int {f\left( x \right)dx}  = {f_0}
$$

$$
\int {f\left( x \right)\prod\limits_{k \ne i} {d{x_k}} }  = {f_0} + {f_i}\left( {{x_i}} \right)
$$

$$
\int {f\left( x \right)\prod\limits_{k \ne i,j} {d{x_k}} }  = {f_0} + {f_i}\left( {{x_i}} \right) + {f_j}\left( {{x_j}} \right) + {f_{ij}}\left( {{x_{ij}}} \right)
$$

根据以上性质，我们可以写出：

$$
\int {{f^2}dx}  - f_0^2 = \sum\limits_{s = 1}^n {\sum\limits_{{i_1} <  \cdots  < {i_s}}^n {\int {f_{{i_1} \cdots {i_s}}^2d{x_{{i_1}}} \cdots d{x_{{i_s}}}} } }
$$

记方差为

$$
D = \int {{f^2}dx}  - f_0^2
$$

和

$$
{D_{{i_1} \cdots {i_s}}} = \int {f_{{i_1} \cdots {i_s}}^2d{x_{{i_1}}} \cdots d{x_{{i_s}}}}
$$

那么有

$$
D = \sum\limits_{s = 1}^n {\sum\limits_{{i_1} < {i_s}}^n {{D_{{i_1} \cdots {i_s}}}} }
$$

将这些项称作方差的原因很简单：如果$x$是在$I^n$上均匀分布的随机变量，那么$f\left(x\right)$和$f_{{i_1} \cdots {i_s}}\left(x_{i_1},\cdots,x_{i_s}\right)$就是方差为$D$和$D_{i_1 \cdots i_s}$的随机变量。

## 敏感度指数

定义：

$$
{S_{{i_1} \cdots {i_s}}} = \frac{{{D_{{i_1} \cdots {i_s}}}}}{D}
$$

为全局敏感度指数。所有的敏感度${S_{{i_1} \cdots {i_s}}}$都是非负的，并且满足

$$
\sum\limits_{s = 1}^n {\sum\limits_{{i_1} <  \cdots  < {i_s}}^n {{S_{{i_1} \cdots {i_s}}}} }  = 1
$$
