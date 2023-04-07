# Deep Evidential Regression

> 参考文献："Deep Evidential Regression"，Alexander Amini等著

## 证据神经网络的思路

**传统BNN面临的问题**：

- 难以直接推断权重的后验分布（训练复杂度高）
- 需要多次推理来获得预测值和预测不确定度（计算复杂度高）
- 如何选择权重的先验仍是一个问题

可以发现，BNN问题的主要来源，是由于**BNN通过学习权重的分布来间接地获得预测值的分布**，但是在实际应用中我们往往仅关心预测的分布。因此，证据神经网络另辟蹊径，直接学习预测值的分布。实际上，证据神经网络学习**“高阶分布”**（我将其理解为“分布的分布”），通过高阶分布捕捉模型不确定性。证据神经网络的主要思路如下：

- 直接为似然设置先验（在证据理论中将这一先验叫做证据分布，它是高阶分布）。

- 通过训练神经网络来输出证据网络的超参数。

- 在给定输入时，通过证据分布的形式和超参数可以直接得到模型不确定性和随机不确定性度量。

下面用回归的例子来具体说明证据神经网络的思路和训练方法。

## 深度证据回归

### 问题设置

我们假设观测到的某一个标签$y_i$是从高斯分布$\mathcal{N}\left( {\mu ,{\sigma ^2}} \right)$采样得到的，即：
$$
{y_i} \sim \mathcal{N}\left( {\mu ,{\sigma ^2}} \right)
$$
在MLE中，我们通过最小化负对数似然来学习模型，损失函数可写为：
$$
{L_i} =  - \log p\left( {{y_i}\left| {\mu ,{\sigma ^2}} \right.} \right) = \frac{1}{2}\log \left( {2\pi {\sigma ^2}} \right) + \frac{{{{\left( {{y_i} - \mu } \right)}^2}}}{{2{\sigma ^2}}}
$$
这里为了表述方便，式子中省去了输入${\mathbf x}_i$和模型参数$\mathbf w$。

### 度量不确定性的思路

可以发现，MLE的损失函数已经能够用${{\sigma ^2}}$来捕捉标签的噪声（随机不确定性），但是无法捕捉模型不确定性。我们可以这么理解：似然$p\left( {{y_i}\left| {\mu ,{\sigma ^2}} \right.} \right)$反映了在给定$\left( {\mu ,{\sigma ^2}} \right)$时标签的分布，也即随机不确定性。而$\left( {\mu ,{\sigma ^2}} \right)$是模型的输出结果，仅仅对$\left( {\mu ,{\sigma ^2}} \right)$采取点估计显然是无法捕捉模型不确定度的。

> 此处做一个补充：一般传统的DNN在做回归问题时，仅将预测值作为输出。相当于模型仅预测$\mu $，而不预测$\sigma ^2$（将$\sigma ^2$认为是一个固定的常数），这样MLE损失函数就退化为了MSE损失函数，模型也无法捕捉随机不确定性。
>
> 但是我们完全可以将$\sigma ^2$也作为网络的一个输出。这样至少有两点好处，一是可以度量随机不确定度，二是通过预测$\sigma ^2$相当于对损失函数添加了关于随机不确定性的正则化：当随机不确定性高时，损失函数中来自MSE误差的一项$\frac{{{{\left( {{y_i} - \mu } \right)}^2}}}{{2{\sigma ^2}}}$会减小，而与数据无关的一项$\frac{1}{2}\log \left( {2\pi {\sigma ^2}} \right)$会增大。也就是说，在模型认为数据的随机不确定性高、数据不太可靠时，模型会自动地减少该数据对训练的贡献，转而更多地去修正关于随机不确定性的预测。

对$\left( {\mu ,{\sigma ^2}} \right)$采取点估计不能捕捉模型不确定性，我们很自然的想到，能否通过预测$\left( {\mu ,{\sigma ^2}} \right)$的分布来捕捉模型不确定性呢？证据神经网络说明了这个思路是可行的。

### 设置共轭先验，得到似然的显式解

证据神经网络在回归问题中，为$\left( {\mu ,{\sigma ^2}} \right)$设置先验$q\left( {\mu ,{\sigma ^2}} \right)$，并且设该先验分布可分，即$q\left( {\mu ,{\sigma ^2}} \right) = q\left( \mu  \right)q\left( {{\sigma ^2}} \right)$。我们设$\mu $的先验分布为${q\left( {\mu ;{{\mathbf{m}}_\mu }} \right)}$，其中${{{\mathbf{m}}_\mu }}$是分布的控制参数；设$\sigma^2$的先验分布${q\left( {{\sigma ^2};{{\mathbf{m}}_\sigma }} \right)}$。

我们同样期望通过最小化似然来训练模型，似然可以写为：
$$
p\left( {{y_i}\left| {{{\mathbf{m}}_\mu },{{\mathbf{m}}_\sigma }} \right.} \right) = \iint {p\left( {{y_i}\left| {\mu ,{\sigma ^2},{{\mathbf{m}}_\mu },{{\mathbf{m}}_\sigma }} \right.} \right)q\left( {\mu ,{\sigma ^2}\left| {{{\mathbf{m}}_\mu },{{\mathbf{m}}_\sigma }} \right.} \right)d\mu d{\sigma ^2}}
$$
一般而言，这个积分难以求得。但是在将$\left( {\mu ,{\sigma ^2}} \right)$的先验设为共轭先验，即正态逆伽马分布时，该积分存在显式解。具体的先验设置为：
$$
\begin{gathered}
  q\left( \mu  \right) = \mathcal{N}\left( {\gamma ,{\sigma ^2}{\upsilon ^{ - 1}}} \right) \hfill \\
  q\left( {{\sigma ^2}} \right) = {\Gamma ^{ - 1}}\left( {\alpha ,\beta } \right) \hfill \\
  q\left( {\mu ,{\sigma ^2}} \right) = q\left( \mu  \right)q\left( {{\sigma ^2}} \right) \hfill \\ 
\end{gathered}
$$
其中${\Gamma ^{ - 1}}$表示逆伽马分布。在这种共轭先验下，似然的显式解为：
$$
p\left( {{y_i}\left| {{{\mathbf{m}}_\mu },{{\mathbf{m}}_\sigma }} \right.} \right) = St\left( {{y_i};\gamma ,\frac{{\beta \left( {1 + \upsilon } \right)}}{{\upsilon \alpha }},2\alpha } \right)
$$
其中$St$表示学生t-分布。

由此，我们可以对似然取负对数得到损失函数：
$$
L_i^{NLL} = \frac{1}{2}\log \left( {2\pi \frac{{\beta \left( {1 + \upsilon } \right)}}{\upsilon }} \right) + \left( {\alpha  + \frac{1}{2}} \right)\log \left( {\frac{{{{\left( {{y_i} - \gamma } \right)}^2}}}{2}\frac{\upsilon }{{\beta \left( {1 + \upsilon } \right)}} + 1} \right) + \log \left( {\frac{{\Gamma \left( \alpha  \right)}}{{\Gamma \left( {\alpha  + \frac{1}{2}} \right)}}} \right)
$$

### 为损失函数添加正则项以避免分布退化

> 在原作者的解释中，正则项是为了"减少在错误样本上的证据"。我下面尝试从loss退化的角度说明添加正则项的必要性。

我们分析得到的损失函数：首先，随着模型不断拟合训练数据，可以预见${{{\left( {{y_i} - \gamma } \right)}^2}}$将越来越小，我们记${\varepsilon_i  = {{\left( {{y_i} - \gamma } \right)}^2}}$。当${\varepsilon_i  \to 0}$，损失函数可以简化为
$$
L_{\varepsilon_i  \to 0}^{NLL} = \frac{1}{2}\log \left( {2\pi \frac{{\beta \left( {1 + \upsilon } \right)}}{\upsilon }} \right)+\log \left( {\frac{{\Gamma \left( \alpha  \right)}}{{\Gamma \left( {\alpha  + \frac{1}{2}} \right)}}} \right)
$$
此时，要想令损失最小，很容易看出将有${\upsilon  \to \infty }$和${\alpha  \to \infty }$，${\upsilon  \to \infty }$将导致$\mu$的方差趋近于0，整个分布收缩于单点，${\alpha  \to \infty }$也将导致$\sigma^2$的分布收缩于单点（$\sigma^2$的分布服从逆伽马分布$IG\left( {\alpha ,\beta } \right)$，直观的收缩过程见下图）。

![不同alpha值对应的逆Gamma分布密度函数图像](https://img-blog.csdn.net/20180407165809537?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzQxODc1MDUy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

也就是说，仅最小化$L_i^{NLL}$将导致$\mu$和$\sigma^2$的分布退化，变为单点估计，最终导致模型"过于自信"，无法有效度量模型不确定性。因此我们需要额外的正则项来避免退化。

文中提出了一种启发式的正则化项：
$$
L_i^R = \left| {{y_i} - \gamma } \right| \cdot \left( {2\upsilon  + \alpha } \right)
$$
由于$\left| {{y_i} - \gamma } \right|$是正数，该正则化项约束了$\upsilon$和$\alpha$不能过大，$\left| {{y_i} - \gamma } \right|$一项起到的效果为：当网络能够很准确地预测时，$\left| {{y_i} - \gamma } \right|$很小，该正则项对训练的影响也很小，也就是说当模型预测很准确时允许模型很自信；而当网络能够不能很准确地预测时，$\left| {{y_i} - \gamma } \right|$很大，此时正则项就会约束模型不能学习到过大的$\upsilon$和$\alpha$，即限制了退化。

### 带有正则项的损失函数

最终，所设计的损失函数为：
$$
{L_i} = L_i^{NLL} + \lambda L_i^R
$$
其中$\lambda$是正则项系数。

### 预测和不确定性的计算式

预测：$\mathbb{E}\left[ \mu  \right] = \gamma $

随机不确定性：$\mathbb{E}\left[ {{\sigma ^2}} \right] = \frac{\beta }{{\alpha  - 1}}$

模型不确定性：$\operatorname{Var} \left[ \mu  \right] = \frac{\beta }{{\upsilon \left( {\alpha  - 1} \right)}}$

## 仿真结果

![image-20221102114513219](C:\Users\WarmyWind\AppData\Roaming\Typora\typora-user-images\image-20221102114513219.png)

对比带有正则项和不带正则项的仿真结果（图B后两个），可以看出，只有在带有正则项时才能捕捉到模型不确定度，与前面的分析相符。

而正则项系数$\lambda$对训练和模型不确定性度量的影响非常大，作者给出了下面这个仿真结果：

![image-20221102115201151](C:\Users\WarmyWind\AppData\Roaming\Typora\typora-user-images\image-20221102115201151.png)