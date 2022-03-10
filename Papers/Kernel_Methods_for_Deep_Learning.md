# Kernel Methods for Deep Learning

这篇文章导出了以ReLU为激活函数的BNN与核的对应关系。

## Arc-cosine kernels

首先给出反余弦核

$$
{k_n}\left( {{\bf{x}},{\bf{y}}} \right) = 2\int {\frac{{{e^{ - \frac{{{{\left\| \bf w \right\|}^2}}}{2}}}}}{{{{\left( {2\pi } \right)}^{{d/2}}}}}\Theta \left( {{\bf{w}} \cdot {\bf{x}}} \right)\Theta \left( {{\bf{w}} \cdot {\bf{y}}} \right){{\left( {{\bf{w}} \cdot {\bf{x}}} \right)}^n}{{\left( {{\bf{w}} \cdot {\bf{y}}} \right)}^n}d{\bf{w}}}
$$

其中

$$
\Theta \left( z \right) = \frac{1}{2}\left( {1 + {{\rm sign}} \left( z \right)} \right)
$$

> 定义向量之间的夹角为
>
> $$
> \theta  = {\cos ^{ - 1}}\left( {\frac{{{\bf{x}} \cdot {\bf{y}}}}{{\left\| {\bf{x}} \right\|\left\| {\bf{y}} \right\|}}} \right)
> $$
>
> 反余弦和可以写为
>
> $$
> {k_n}\left( {{\bf{x}},{\bf{y}}} \right) = \frac{1}{\pi }{\left\| {\bf{x}} \right\|^n}{\left\| {\bf{y}} \right\|^n}{J_n}\left( \theta  \right)
> $$
>
> 其中
>
> $$
> {J_n}\left( \theta  \right) = {\left( { - 1} \right)^n}{\left( {\sin \theta } \right)^{2n + 1}}{\left( {\frac{1}{{\sin \theta }}\frac{\partial }{{\partial \theta }}} \right)^n}\left( {\frac{{\pi  - \theta }}{{\sin \theta }}} \right)
> $$
>
> 前两个个为
>
> $$
> {J_0}\left( \theta  \right) = \pi  - \theta
> $$
>
> $$
> {J_1}\left( \theta  \right) = \sin \theta  + \left( {\pi  - \theta } \right)\cos \theta
> $$

我们将看到这种核与以权重先验分布为高斯分布，且以ReLU为激活函数的神经网络有紧密联系。

## Computation in single-layer threshold networks

设单隐藏层神经网络的输出维度为$m$，激活函数的形式为$h\left( z \right) = \Theta \left( z \right){z^n}$，那么对于具有权重${\bf W}=\left({\bf w}_1,\cdots,{\bf w}_m\right)$网络$\bm f$，输出为

$$
{\bm f}\left( {\bf x} \right) = {\left( {\Theta \left( {{{\bf{w}}_1} \cdot {\bf{x}}} \right){{\left( {{{\bf{w}}_1} \cdot {\bf{x}}} \right)}^n}, \cdots ,\Theta \left( {{{\bf{w}}_m} \cdot {\bf{x}}} \right){{\left( {{{\bf{w}}_m} \cdot {\bf{x}}} \right)}^n}} \right)^T}
$$

因此对于输入$\bf x$和$\bf y$，输出的点积（相关性）为

$$
{\bm f}\left( {\bf{x}} \right) \cdot {\bm f}\left( {\bf{y}} \right) = \sum\limits_{i = 1}^m {\Theta \left( {{{\bf{w}}_i} \cdot {\bf{x}}} \right){{\left( {{{\bf{w}}_i} \cdot {\bf{x}}} \right)}^n}\Theta \left( {{{\bf{w}}_i} \cdot {\bf{x}}} \right){{\left( {{{\bf{w}}_i} \cdot {\bf{x}}} \right)}^n}}
$$

我们进一步假设权重独立且服从标准正态分布，即具有零均值和单位方差，在$m\to \infty$时，满足

$$
\begin{aligned}
\lim \limits_{m \to \infty } \frac{2}{m}f\left( {\bf{x}} \right) \cdot f\left( {\bf{y}} \right) 
&= \lim \limits_{m \to \infty } \frac{2}{m}\sum\limits_{i = 1}^m {\Theta \left( {{{\bf{w}}_i} \cdot {\bf{x}}} \right){{\left( {{{\bf{w}}_i} \cdot {\bf{x}}} \right)}^n}\Theta \left( {{{\bf{w}}_i} \cdot {\bf{y}}} \right){{\left( {{{\bf{w}}_i} \cdot {\bf{y}}} \right)}^n}} \\ 
&= 2{E_{\bf{w}}}\left[ {\Theta \left( {{\bf{w}} \cdot {\bf{x}}} \right){{\left( {{\bf{w}} \cdot {\bf{x}}} \right)}^n}\Theta \left( {{\bf{w}} \cdot {\bf{y}}} \right){{\left( {{\bf{w}} \cdot {\bf{y}}} \right)}^n}} \right]\\ 
&= 2\int \frac{{{e^{ - \frac{{{{\left\| {\bf{w}} \right\|}^2}}}{2}}}}}{{{{\left( {2\pi } \right)}^{d/2}}}}\Theta \left( {{\bf{w}} \cdot {\bf{x}}} \right)\Theta \left( {{\bf{w}} \cdot {\bf{y}}} \right){{\left( {{\bf{w}} \cdot {\bf{x}}} \right)}^n}{{\left( {{\bf{w}} \cdot {\bf{y}}} \right)}^n}d{\bf{w}}\\
&= {{k_n}\left( {{\bf{x}},{\bf{y}}}\right)}
\end{aligned}
$$

当$n=1$，相应的激活函数就是ReLU。可见，反余弦核可以被看作无限宽度单隐藏层神经网络的输入在特征空间中的内积。

## Computation in multilayer threshold networks

对于反余弦核，我们有

$$
k_n^{\left( l \right)}\left( {{\bf{x}},{\bf{x}}} \right) = {\phi _{l}}\left( \bf x \right) \cdot{\phi _{l}}\left(\bf x \right) = {\left\| {{\phi _{l}}\left( x \right)} \right\|^{{2}}}
$$

其中$\phi_l\left(\cdot \right)$表示$l$个$\phi \left(\cdot\right)$嵌套而成的函数。因此可以写出核函数的递归形式

$$
k_n^{\left( {l + 1} \right)}\left( {{\bf{x}},{\bf{y}}} \right) = \frac{1}{\pi }{\left[ {k_n^{\left( l \right)}\left( {{\bf{x}},{\bf{x}}} \right)k_n^{\left( l \right)}\left( {{\bf{y}},{\bf{y}}} \right)} \right]^{{n \mathord{\left/
 {\vphantom {n 2}} \right.
 } 2}}}{J_n}\left( {\theta _n^{\left( l \right)}} \right)
$$

其中

$$
\theta _n^{\left( l \right)} = {\cos ^{ - 1}}\left( {k_n^{\left( l \right)}\left( {{\bf{x}},{\bf{y}}} \right){{\left[ {k_n^{\left( l \right)}\left( {{\bf{x}},{\bf{x}}} \right)k_n^{\left( l \right)}\left( {{\bf{y}},{\bf{y}}} \right)} \right]}^{{{ - 1} \mathord{\left/
 {\vphantom {{ - 1} 2}} \right.
 } 2}}}} \right)
$$
