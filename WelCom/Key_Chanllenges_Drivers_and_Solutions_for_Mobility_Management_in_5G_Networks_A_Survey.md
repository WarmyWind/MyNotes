# Key Challenges, Drivers and Solutions for Mobility Management in 5G Networks: A Survey

## Mobility Overview

无线网络中的移动性，是指在理想情况下，UE在小区之间移动过程中保持与无线网络连接的能力。

**移动性功能**在无线通信网络中对支持移动性起着关键作用。目前有一些移动性功能，包括移动互联网协议（IP）、切换决策、切换优化功能记忆重路由移动协议。
**移动性速度**的不同对UE移动中的接收信号强度有不同的影响，进一步导致对通信可靠性的不同影响。不同通信系统所支持的最大移动性速度也不同，比如4G最大支持350km/h的速度，而5G最大支持500km/h。
**高效的移动性技术**是指移动性过程、方法、机制和协议能够有效地支持可靠稳定的移动性连接。

- 切换自优化功能：调节移动性控制参数的技术。
- 切换决策算法：用于决定切换的机制。
- 移动性路由协议：用于重路由数据或语音到新路由路径的协议。

## Key Factors For Mobility Challenges

### Dual Connectivity(DC)

DC使得UE可以同时连接到两个小区，UE可以同时与4G和5G网络通信。DC增加了切换的场景。

### Carrier Aggregation

载波聚合（carrier aggregation，CA）同样增加了切换场景，比如从CC到CC的切换，这种切换发生在系统要求改变PCC时。

### Inefficient Optimization Process

切换参数优化（handover parameter optimization，HPO）功能是3GPP提出的4G和5G的基础特性之一。HPO利用瞬时的网络状态来调整切换控制参数（handover control parameter，HCP）。但是根据研究，算法只能为HCP提供有效的优化，不存在最优解。目前的5G移动网络中仅有非鲁邦、次优的HPO算法，其中大部分算法是为了4G技术设计的，因此在5G场景中这些算法很可能是低效的。

### Central Self-Optimization Operation

在5G系统中，由于UE的高移动速度和超可靠通信的需要，中央优化不会是最优解。因此需要去中心化和分布式的方法。

### Partial Self-Optimization

部分自由化指优化算法仅对一部分HCP进行。比如一些算法仅优化HOM，而TTT被固定。

### Inefficient Handover Decision Algorithm

目前的需要对切换决策算法做更多的调查和发展。
