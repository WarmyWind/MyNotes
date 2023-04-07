# 通过MCMC训练BNN

> 主要参考《Beyesian Learning for Neural Networks》，Neal著。该文是1995年Neal写的博士论文，详细阐释了通过MCMC训练BNN的经典方法。

## MCMC

在BNN中，给定输入$x^*$，其对应的预测值$y^*$是一个随机变量。我们常取预测的期望$\mathbb{E}\left[ {{y^*}} \right]$作为预测值的点估计，取方差${\text{Var}}\left[ {{y^*}} \right] = \mathbb{E}\left[ {{{\left( {{y^*} - \mathbb{E}\left[ {{y^*}} \right]} \right)}^2}} \right]$作为预测不确定性的度量，此处注意方差也是一种“期望值”。蒙特卡洛方法就是通过一系列采样来近似期望值，记所要估计的期望值为${\mathbb{E}}\left[ {a\left( \theta  \right)} \right]$，那么蒙特卡洛采样可将其近似为：
$$
{\mathbb{E}}\left[ {a\left( \theta  \right)} \right] \approx \frac{1}{N}\sum\limits_{t = 1}^N {a\left( {{\theta ^{\left( t \right)}}} \right)}
$$
其中${\theta ^{\left( 1 \right)}}, \cdots ,{\theta ^{\left( t \right)}}$是采样过程得到的采样点，每个采样点都服从分布$Q$。在简单蒙特卡洛方法中，${\theta ^{\left( 1 \right)}}, \cdots ,{\theta ^{\left( t \right)}}$是相互独立的，但当分布$Q$是较为复杂的分布时，直接生成独立的采样点往往是不可行的。

当${\theta ^{\left( 1 \right)}}, \cdots ,{\theta ^{\left( t \right)}}$不独立时，蒙特卡洛近似依然成立。因此我们可以考虑将采样过程视作马尔可夫过程，我们将转移概率记为$T\left( {{\theta ^{\left( {t + 1} \right)}}\left| {{\theta ^{\left( t \right)}}} \right.} \right)$，$\theta$的分布记为$Q$，那么马尔可夫链的细致平稳条件为：
$$
T\left( {\theta '\left| \theta  \right.} \right)Q\left( \theta  \right) = T\left( {\theta \left| {\theta '} \right.} \right)Q\left( {\theta '} \right)
$$
只要满足细致平稳条件，则$Q$是稳态分布。一个遍历的马尔科夫链具有唯一的一个稳态分布，从任何初始状态出发都能收敛到它。因此我们只要设计转移概率，使得稳态分布为所要采样的目标分布，从任意初始状态出发经过足够多次的转移，之后每一次的采样都满足目标分布。

## Gibbs采样

Gibbs采样是MCMC中最容易实现的方法，在1984年由Geman提出，之后被应用于玻尔兹曼机。

Gibbs采样在高维的情况下适用。我们设要采样的参数是高维的，$\theta  = \left\{ {{\theta _1}, \cdots ,{\theta _p}} \right\}$，服从分布$Q$但无法直接从$Q$采样。但Gibbs采样假设，我们对于成分${\theta _j},\forall j$，在给定其他成分时，我们可以从条件概率分布$p\left( {{\theta _j}\left| {\left\{ {{\theta _{i \ne j}}} \right\}} \right.} \right)$生成${\theta _j}$。由${\theta ^{\left( t \right)}}$采样${\theta ^{\left( {t + 1} \right)}}$可以分为$p$步：
$$
\begin{array}{*{20}{c}}
  {{\text{pick}}\;\theta _1^{\left( {t + 1} \right)} \sim p\left( {{\theta _1}\left| {\theta _2^{\left( t \right)},\theta _3^{\left( t \right)}, \cdots ,\theta _p^{\left( t \right)}} \right.} \right)} \\ 
  {{\text{pick}}\;\theta _2^{\left( {t + 1} \right)} \sim p\left( {{\theta _2}\left| {\theta _1^{\left( {t + 1} \right)},\theta _3^{\left( t \right)}, \cdots ,\theta _p^{\left( t \right)}} \right.} \right)} \\ 
   \vdots  \\ 
  {{\text{pick}}\;\theta _p^{\left( {t + 1} \right)} \sim p\left( {{\theta _2}\left| {\theta _1^{\left( {t + 1} \right)},\theta _2^{\left( {t + 1} \right)}, \cdots ,\theta _{p - 1}^{\left( {t + 1} \right)}} \right.} \right)} 
\end{array}
$$
对于贝叶斯学习而言，能否简单地由条件概率采样决定了Gibbs采样是否可用。对于一些统计问题，这些条件分布是标准的正则形式，但是对于神经网络，在给定其他权重时，某个权重的条件分布往往十分复杂，可能具有多种模式。但是，Gibbs采样是混合蒙特卡洛（Hybrid Monte Carlo，HMC）算法的组成部分，HMC可以用于神经网络。

## Metropolis算法

通过Metropolis算法定义马尔科夫链，可以简单地构造出使得马尔可夫链以$Q$为稳态分布的转移概率。Metropolis算法中，从${\theta ^{\left( t \right)}}$到${\theta ^{\left( {t + 1} \right)}}$的转移方法为：

1. 从提议分布$S\left( {{\theta ^*}\left| {{\theta ^{\left( t \right)}}} \right.} \right)$中生成一个候选状态${{\theta ^*}}$。
2. 以概率$\min \left( {1,\frac{{Q\left( {{\theta ^*}} \right)}}{{Q\left( {{\theta ^{\left( t \right)}}} \right)}}} \right)$接受候选状态。
3. 如果接受候选状态，则令${\theta ^{\left( {t + 1} \right)}} = {\theta ^*}$，否则${\theta ^{\left( {t + 1} \right)}} = {\theta ^{\left( t \right)}}$。

此时，转移概率为：
$$
T\left( {\theta '\left| \theta  \right.} \right) = S\left( {\theta '\left| \theta  \right.} \right)\min \left( {1,\frac{{Q\left( {\theta '} \right)}}{{Q\left( \theta  \right)}}} \right)
$$
为了保证细致平稳，提议分布$S$必须是对称的，即$S\left( {\theta '\left| \theta  \right.} \right) = S\left( {\theta \left| {\theta '} \right.} \right)$，因为此时：
$$
\begin{aligned}
  T\left( {\theta '\left| \theta  \right.} \right)Q\left( \theta  \right) 
  &= S\left( {\theta '\left| \theta  \right.} \right)\min \left( {1,\frac{{Q\left( {\theta '} \right)}}{{Q\left( \theta  \right)}}} \right)Q\left( \theta  \right) \hfill \\
   &= S\left( {\theta '\left| \theta  \right.} \right)\min \left( {Q\left( \theta  \right),Q\left( {\theta '} \right)} \right) \hfill \\
   &= S\left( {\theta \left| {\theta '} \right.} \right)\min \left( {Q\left( {\theta '} \right),Q\left( \theta  \right)} \right) \hfill \\
   &= T\left( {\theta \left| {\theta '} \right.} \right)Q\left( {\theta '} \right) \hfill \\ 
\end{aligned} 
$$

## 混合蒙特卡洛算法

一种理解HMC的方法是将其看作是Gibbs采样和特别复杂的Metropolis算法的结合。

### 用能量概念表述问题

HMC算法起先是在物理领域被提出，用于从正则（玻尔兹曼）分布采样一个物理系统的状态，而玻尔兹曼分布由能量方程定义。虽然如此，但HMC实际上可以用于任何可导的概率密度，并且在非物理语境下，通过用一个虚拟物理系统的能量函数来表述问题，保留物理术语也是很便于理解的。

假设我们希望从某个分布采样“位置”变量$q$，并且$q$有$n$维$\left\{ {{q_1}, \cdots ,{q_n}} \right\}$，在实际的物理系统中，$q$对应着所有例子的坐标，而在我们的应用中，$q$代表网络权重集合。在玻尔兹曼分布的定义下，概率密度为：
$$
P\left( q \right) \propto \exp \left( { - E\left( q \right)} \right)
$$
其中${E\left( q \right)}$是“势能”函数。任何无零点的概率密度函数都可以写为这种形式。

为了使用动力学方法，我们进一步引入“动量”变量$p$，$p$同样有$n$维$\left\{ {{p_1}, \cdots ,{p_n}} \right\}$并且与$q$一一对应。在“相空间”中正则分布被定义为：
$$
P\left( q,p \right) \propto \exp \left( { - H\left( {q,p} \right)} \right)
$$
其中$H\left( {q,p} \right) = E\left( q \right) + K\left( p \right)$被称为哈密顿函数，$K\left( p \right)$是与动量相关的动能函数，通常的选择是：
$$
K\left( p \right) = \sum\limits_{i = 1}^n {\frac{{p_i^2}}{{2{m_i}}}} 
$$
其中$m_i$是“质量”。

我们希望采样的分布是$P\left( q \right)$，而在$P\left( {q,p} \right)$中$q$和$p$是独立的，其关于$q$的边缘分布和$P\left( q \right)$一致。因此我们可以定义一个稳态分布为$P\left( {q,p} \right)$的马尔科夫链，然后在采样时简单地忽略$p$即可。

### 随机动力学方法

HMC可以被视作随机动力学方法的一种具体应用。随机动力学方法中，从$P\left( {q,p} \right)$采样$q$和$p$被分为两个子任务：从有固定总能量$H\left( {q,p} \right)$的$q$和$p$的值中均匀采样，并从不同$H$的值中采样状态。

在哈密顿动力学中，位置$q$和动量$p$关于虚拟时间$\tau$的变化率满足哈密顿方程：
$$
\begin{gathered}
  \frac{{d{q_i}}}{{d\tau }} =  + \frac{{\partial H}}{{\partial {p_i}}} = \frac{{{p_i}}}{{{m_i}}} \hfill \\
  \frac{{d{p_i}}}{{d\tau }} =  - \frac{{\partial H}}{{\partial {q_i}}} =  - \frac{E}{{\partial {q_i}}} \hfill \\ 
\end{gathered} 
$$
哈密顿动力学有三个至关重要的性质。首先，总能量$H$是守恒的，不随时间变化：
$$
\frac{{dH}}{{d\tau }} = \sum\limits_i {\left[ {\frac{{\partial H}}{{\partial {q_i}}}\frac{{d {q_i}}}{{d \tau }} + \frac{{\partial H}}{{\partial {p_i}}}\frac{{d {p_i}}}{{d\tau }}} \right]}  = \sum\limits_i {\left[ {\frac{{\partial H}}{{\partial {q_i}}}\frac{{\partial H}}{{\partial {p_i}}} - \frac{{\partial H}}{{\partial {p_i}}}\frac{{\partial H}}{{\partial {q_i}}}} \right]}  = 0
$$
其次，给定空间$\left(q,p\right)$，相速度的散度为0，根据刘维尔定理，相空间的体积守恒：
$$
\sum\limits_i {\left[ {\frac{\partial }{{\partial {q_i}}}\left( {\frac{{d{q_i}}}{{d\tau }}} \right) + \frac{\partial }{{\partial {p_i}}}\left( {\frac{{d{p_i}}}{{d\tau }}} \right)} \right]}  = \sum\limits_i {\left[ {\frac{\partial }{{\partial {q_i}}}\frac{{\partial H}}{{\partial {p_i}}} - \frac{\partial }{{\partial {p_i}}}\frac{{\partial H}}{{\partial {q_i}}}} \right]}  = 0
$$
最后，哈密顿动力学是可逆的。在根据动力学前向运动一段时间后，可以通过反向运动恢复初始状态。

上述性质说明，对于服从哈密顿动力学路径发生的转移，$q$和$p$的正则分布$P\left( q,p \right)$是不变的。状态最终转移到某个小区域的概率，和从通过反向运动找到的对应初始状态开始的概率相同。

按照哈密顿动力学，给定总能量$H$的值，$q$和$p$通常能够探索所有满足总能量为$H$的状态。但是这种转移性质不足以构造一个遍历的马尔科夫链，因为不同$H$的状态将无法探索。

在随机动力学方法中，一个遍历的马尔科夫链通过交替实行确定性的动力学转移和随机Gibbs采样更新动量来实现。由于$p$与$q$相独立，并且$p$服从的$\exp \left( { - K\left( p \right)} \right)$是高斯分布，对$p$采样很容易。对$p$的更新将改变$H$，使得状态有机会探索整个相空间。

虚拟时间$\tau$的长度是可调整的参数，最好是调整到使得路径上每一个状态点的$q$的变化都很大，这将有助于避免随机游走效应。

在实际应用中，哈密顿动力学不能精确地模拟，只能用离散的优先时间步来近似。在常见的leapfrog方法中，每一次迭代都计算位置和动量的近似，从时间$\tau$到$\tau+\epsilon$的转移过程如下：
$$
\begin{aligned}
  {{\hat p}_i}\left( {\tau  + \frac{\varepsilon }{2}} \right) 
  &= {{\hat p}_i}\left( \tau  \right) - \frac{\varepsilon }{2}\frac{{\partial E\left( {\hat q\left( \tau  \right)} \right)}}{{\partial {q_i}}} \hfill \\
  {{\hat q}_i}\left( {\tau  + \varepsilon } \right) 
  &= {{\hat q}_i}\left( \tau  \right) + \varepsilon \frac{{{{\hat p}_i}\left( {\tau  + \frac{\varepsilon }{2}} \right)}}{{{m_i}}} \hfill \\
  {{\hat p}_i}\left( {\tau  + \varepsilon } \right) 
  &= {{\hat p}_i}\left( {\tau  + \frac{\varepsilon }{2}} \right) - \frac{\varepsilon }{2}\frac{{\partial E\left( {\hat q\left( {\tau  + \varepsilon } \right)} \right)}}{{\partial {q_i}}} \hfill \\ 
\end{aligned}
$$
在leapfrog离散化中，相空间的体积依然守恒，并且动力学过程也可逆，但是总能量$H$不再精确地守恒。只有当$\varepsilon  \to 0$，误差才趋于0，因此会不可避免地带有系统误差。HMC算法通过Metropolis算法消除系统误差。

### HMC

HMC算法通过Metropolis算法消除系统误差。其算法主要流程为：

![image-20221110215414597](C:\Users\WarmyWind\AppData\Roaming\Typora\typora-user-images\image-20221110215414597.png)

HMC算法通过引入接受概率来修正对时间离散化造成的系统误差。当$L=1$的HMC算法又称为Langevin Monte Carlo。

在贝叶斯推断中，我们关注的是模型参数的后验分布，因此这些参数就扮演了“位置”$q$的角色。我们可以定义如下的能量函数：
$$
E\left( q \right) =  - \log \left[ {\pi \left( q \right)L\left( {D\left| q\right.} \right)} \right]
$$
其中${\pi \left( q \right)}$是先验，${L\left( {D\left| q\right.} \right)}$是似然。

### HMC保证正则分布不变的证明

我们将$\left(q,p\right)$空间划分为不同的区域，记区域为$A_k$，每个区域有相同的体积$V$。将$A_k$经过$L$步leapfrop后转移到的象（image）记为$B_k$，$B_k$的体积同样为$V$。

我们想要证明细致平衡条件：
$$
P\left( {{A_i}} \right)T\left( {{B_j}\left| {{A_i}} \right.} \right) = P\left( {{B_j}} \right)T\left( {{A_i}\left| {{B_j}} \right.} \right)
$$
当$i \ne j$，显然$T\left( {{B_j}\left| {{A_i}} \right.} \right) = T\left( {{A_i}\left| {{B_j}} \right.} \right) = 0$，等式成立。当$i=j=k$，可以写出
$$
\frac{V}{Z}\exp \left( { - {H_{{A_k}}}} \right)\min \left[ {1,\exp \left( { - {H_{{B_k}}} + {H_{{A_k}}}} \right)} \right] = \frac{V}{Z}\exp \left( { - {H_{{B_k}}}} \right)\min \left[ {1,\exp \left( { - {H_{{A_k}}} + {H_{{B_k}}}} \right)} \right]
$$
等式显然成立。