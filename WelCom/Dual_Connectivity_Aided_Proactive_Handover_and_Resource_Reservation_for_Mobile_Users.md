# Dual Connectivity Aided Proactive Handover and Resource Reservation for Mobile Users

## 数据率计算

当$\rho _{k,g}^{f,t,n} = 1$时，即$BS_g$在第t个时隙的第f帧分配第n块RB给$UE_k$，$UE_k$在第n块RB上的瞬时速率为：
$$
r_{k,g}^{f,t,n} = {W_0}{\log _2}\left( {1 + \gamma_{k,g}^{f,t,n}} \right)
$$
其中的信干噪比为：
$$
\gamma _{k,g}^{f,t,n} = {{P_{k,g}^{f,t,n}{{\left| {{{\left( {{\bf{h}}_{k,g}^{f,t,n}} \right)}^H}{\bf{w}}_{k,g}^{f,t,n}} \right|}^2}} \over {I_{k,g}^{f,t,n} + {\sigma ^2}}}
$$
功率$${P_{k,g}^{f,t,n}}$$是$BS_g$给$UE_k$在第n块RB上根据所有的UE数和RB数平均分配的，其计算式为：
$$
P_{k,g}^{f,t,n} = {{{P_{\max }}} \over {\sum\nolimits_{n = 1}^{{N_{tot}}} {\sum\nolimits_{l \in {\cal K}_g^f} {\rho _{l,g}^{f,t,n}} } }}
$$
$${\bf{h}}_{k,g}^{f,t,n} \in {{\Bbb C}^{{N_{tx}} \times 1}}$$是瞬时信道向量，$${\bf{w}}_{k,g}^{f,t,n} \in {{\Bbb C}^{{N_{tx}} \times 1}}$$是预编码向量，满足$$\left\| {{\bf{w}}_{k,g}^{f,t,n}} \right\|=1$$.干扰则满足计算式：
$$
I_{k,g}^{f,t,n} = \sum\limits_{l \in {\cal K}_g^f\backslash k} {\rho _{l,g}^{f,t,n}P_{l,g}^{f,t,n}{{\left| {{{\left( {{\bf{h}}_{k,g}^{f,t,n}} \right)}^H}{\bf{w}}_{l,g}^{f,t,n}} \right|}^2}}  + \sum\limits_{g' \in {{\cal G}}\backslash g} {\sum\limits_{l' \in {{\cal K}}_{g'}^f} {\rho _{l',g'}^{f,t,n}P_{l',g'}^{f,t,n}{{\left| {{{\left( {{\bf{h}}_{k,g'}^{f,t,n}} \right)}^H}{\bf{w}}_{l',g'}^{f,t,n}} \right|}^2}} }
$$
 干扰的第一项是不同UE之间产生的干扰，第二项是同一个UE接入不同小区而产生的干扰（若不考虑双连接，则这一项去掉）。

由此，$UE_k$在第t个时隙的第f帧，由$BS_g$提供的和数据率为：
$$
R_{k,g}^{f,t} = \sum\limits_{n = 1}^{{N_{tot}}} {\rho _{k,g}^{f,t,n}r_{k,g}^{f,t,n}}
$$
记$${\cal C}_k^f$$表示在第f帧$UE_k$连接的BS集合，在双连接下，$UE_k$能获得的和数据率为：
$$
R_k^{f,t} = \sum\limits_{g \in {\cal C}_k^f} {R_{k,g}^{f,t}} 
$$


## 预测资源留存

在第f-1帧结束时，在已经得到所有UE接入的BS预测的前提下（即已经知道$$\left\{ {\hat {\cal C}_k^f} \right\}$$），中央处理器将优化资源留存参数（即$$\left\{ {\rho _{k,g}^{f,t,n}} \right\}$$）来保证在第f帧的每个时隙都能保证UE的QoS需求。

为了反映网络吞吐量和用户公平性，我们最大化网络和速率和最小用户速率的一个加权和，优化目标可以表示为：
$$
\mathop {\max }\limits_{\left\{ {\rho _{k,g}^{f,t,n}} \right\}} \;\omega \frac{1}{{{K^f}}}\sum\limits_{k \in {{\cal K}^f}} {\hat R_k^f}  + \left( {1 - \omega } \right)\mathop {\min }\limits_{k \in {{\cal K}^f}} \left\{ {\hat R_k^f} \right\}
$$
其约束为：
$$
\eqalign{
  & \hat R_k^{f,t} \geqslant R_k^{th},  \cr 
  & \rho _{k,g}^{f,t,n} \in \left\{ {0,1} \right\},\sum\limits_{n = 1}^{{N_{tot}}} {\rho _{k,g}^{f,t,n} \leqslant {N_{tot}}} ,\sum\limits_{k \in {\cal K}_g^f} {\rho _{k,g}^{f,t,n}}  \leqslant {K_{\max }},\sum\limits_{g \in {\cal C}_k^f} {\rho _{k,g}^{f,t,n}}  \leqslant 1 \cr}
$$
直接解该优化问题十分复杂，所以考虑简化问题。首先，在第f帧，SINR的近似估计式子为：
$$
\hat \gamma _{k,g}^f \approx \frac{{P_{k,g}^{f,n}\left( {{N_{tx}} - {K_{\max }} + 1} \right)\hat \alpha _{k,g}^f}}{{\sum\nolimits_{g' \in {\cal G}\backslash {\text{g}}} {\left( {P_{k,g'}^{f,n}{K_{\max }}} \right)\hat \alpha _{k,g'}^f}  + {\sigma ^2}}}
$$
由此，估计的数据率为：
$$
\hat R_{k,g}^f = \bar m_{k,g}^f\hat r_{k,g}^f = \bar m_{k,g}^f{W_0}{\log _2}\left( {1 + \hat \gamma _{k,g}^f} \right)
$$
其中$$\bar m_{k,g}^f = \frac{1}{{{N_s}}}\sum\limits_{t = 1}^{{N_s}} {\sum\limits_{n = 1}^{{N_{tot}}} {\rho _{k,g}^{f,t,n}} } $$，是第f帧中给$UE_k$预分配的平均RB数。
