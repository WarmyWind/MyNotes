# A Data Driven Framework for Inter-Frequency Handover Failure Prediction and Mitigation

## Introduction

在切换（handover，HO）时，从用户设备（user equipment，UE）到基站（base station，BS）会有一系列的信息交换（如测量报告、切换命令、切换确认等）。由于这些信息是通过无线传输的，如果信号状况差的话很容易传输失败，进而导致切换失败（handover failure，HOF）。此外，对切换参数的次优调整也可能导致较低的切换成功率（handover success rate，**HOSR**）。

## System Model

### TORIS Framework

暂略。

### Inter-frequency Handover Success Rate

在目前的4G和5G网络中，主要有两种切换方式，一种是频内切换，另一种是频间切换。

> 频内切换：用户从当前的BS切换到另一个BS，这个切换操作在同一个频带上进行。
>
> 频间切换：用户从当前的BS切换到另一个BS，而且切换为不同的频带。

频间切换常发生在以下情况：UE请求了一个不能在当前频带上获得的服务，或是在跨频带的负载均衡期间请求了服务。

频间HOSR是对于频间切换性能的一种直接度量。HOSR是蜂窝网络最重要的KPI之一，高HOSR表示UE可以无缝地从一个BS切换到另一个BS，因此QoS能得到保证。HOSR$\xi$可以表示为

$$
\xi {\rm{ = }}\frac{{{n_s}}}{{{n_s} + {n_f}}} \times 100\%
$$

其中$n_s$表示切换成功的数量，$n_f$表示切换失败的数量。

### Factors Affecting Handover Success Rate

除了切换参数的次优配置以外，一个影响HOSR的主要因素是信号强度和信号质量。在目前的蜂窝网络中，信号强度通过参考信号接收功率（reference signal received power,RSRP）来反映。基站$x$到用户$u$的下行RSRP$R_u^x$为

$$
R_u^x = {T_x}d_u^x
$$

其中$T$是基站的发射功率，$d$是路径损耗。

在一个蜂窝网络中，信号质量有不同的测量指标。本文中使用G因子，其表达式为

$$
G_u^x = \frac{{R_u^x}}{{\sum\nolimits_{i \in {I_x}} {R_i^x}  + {n_0}}}
$$

其中$I_x$包含了基站$x$的5个最强的干扰源，$n_0$为噪声常数。5个最强的干扰源是通过工业领域知识人为选择的：根据经验，来自5个相邻BS的干扰量对G因子的计算贡献最大，其影响从第6个干扰量开始减小。

> 尽管SINR也是度量型号质量的很好的指标，但SINR对相邻BS的负载瞬时变化很敏感。另外，G因子基本上是SINR的最差情况，因此使用G因子可以涵盖最糟的情况。

另外，文章也考虑了将用户速度作为HOF预测的输入特征。有相关文献表明随着用户速度增加，切换成功率会降低。

## Simulation Setup and Data Generation

从现实的网络中收集数据是不实际的，因为HOF是不定时发生的，要收集足够的HOF样本需要极大量的测试。因此文章通过SyntheticNET仿真器进行数据生成。

## Data Augmentation and Evaluation of Synthetic Data Quality

暂略。

## AI-based Handover Failure Prediction Model

### HOF Prediction Models

文章考虑一下六种分类器：

1. 支持向量分类器
2. 朴素贝叶斯分类器
3. k-近邻算法
4. 随机森林算法
5. 极端梯度增强（extreme gradient boosting，XGBoost）
6. 自适应增强（adaptive boosting，AdaBoost）

文章认为在二分类问题上，与深度神经网络相比，这几种方法的效率和在小数据集上的表现好。

### Domain Knowledge Based Feature Engineering

结合领域知识来选择输入特征可以提高AI模型的性能表现。文章首先进行了Sobol-based方差敏感度分析。

然后根据敏感度分析给出的特征组合为特征集，绘制输入为不同特征集的ROC和PRC曲线（文中似乎没提到用什么分类器），根据曲线下面积来判断特征集是否更有利于正确的预测。
