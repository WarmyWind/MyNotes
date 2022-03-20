# A Survey on Handover Management: From LTE to NR

本文提供了关于从LTE到NR在基本HO场景下的主要不同的综述。

## Introduction

5G系统主要通过减少BS的覆盖范围、增加BS的密度来增加系统容量。这么做的坏处在于会增加HO，并且会增加信令开销，进而干扰数据流、降低用户的吞吐量。因此除了要提供高速率，提供可靠的HO机制对于保证QoE也同样重要。

小基站的低功耗使得他们能被部署在任何地方，并且根据节能的需要可以协调他们的开启与关闭。随着小基站的开启与关闭，移动用户的信道状况和邻小区列表会改变的非常快，这可能会导致干扰增加、能耗增加、HO过于频繁、乒乓效应和延迟增大。如果用户经历多个HO，HO延迟的影响会积累，最终导致用户体验的严重退化。

总的来说，我们可以将无线接入移动性分为两种过程：小区（再）选择和HO。小区（再）选择发生在UE处于空闲模式时，HO发生在UE处于连接模式时。本文主要关注HO。

1G时代的小区覆盖范围为数十公里，在1978年测试了1G的HO过程，包括了UE辅助程序紧接着由网络决策的过程。2G网络的小区大小减小，加重了HO问题。为了提高HO过程的速度，采取了信令开销很大的分布式HO决策，而乒乓效应通过加入一个滞后值（hysteresis）来处理。3G CDMA网络采取了软HO算法。这种软HO算法遵从make-before-break的理念，提高了增益但对其他用户造成了更大干扰，最终干扰超过增益。所以对于4G和5G技术，HO过程重新回到break-before-make方案。更小的小区带来了更小的HO区域，并且没有时间传输过多的HO信令，这要求简明的HO过程。

为了实现较高的移动数据流量，异构网（HetNets）被认为是最有前景的解决方法。HetNets有一些显著的挑战，比如小区间干扰协调（inter-cell-interference-coordination，ICIC）、移动性管理和回程配给（backhaul provisioning）。其中移动性管理格外重要。在异构部署中，UE都用相同的HO参数（比如TTT和hysteresis margin（HM））。但是，用相同的一套参数会降低移动性性能，因此需要自适应的管理方法来提高移动性性能。

### 5G Targets and the Importance od Mobility

5G的目标不仅局限于更高的系统容量、更高的数据率和更广的覆盖范围，还包括比如IoT设备要求的低耗、URLLC等。所以，具有前向兼容性的移动性管理是实现平稳的未来优化的重要设计原则。

### Motivation from an Energy Saving Point of View

电信排放的二氧化碳占总排放量的40%，设计有效的HO过程是减少能耗的一步。

### Main Objectives and Scope of This Paper

第二部分讨论无线接入移动性的相关基础感念。第三和第四部分分别综述LTE和NR中的HO管理。第五部分讨论HO管理的挑战和无线接入移动性增强的未来研究方向。第六部分总结。

## Radio Access Mobility in Cellular Networks: Some General Concepts

1. 在UE辅助的HO控制中，UE一般会通过服务基站（S-BS）发来的一个下行参考信号（reference signal，RS）来测量信号强度（signal strenth，SS）或是信号质量，对于邻基站也是如此。
2. 在测量过后，如果某种特定的情况（进入条件，entry condition）满足了，就向S-BS发送一个测量报告（MR）。
3. 一旦S-BS正确接收到了MR，在目标基站（T-BS）和S-BS之间的HO准备阶段就开始了，（持续TTT收到MR）然后发送HO请求给T-BS。
4. 成功获准后，S-BS发送一个HO命令（HO command，HOcmd）给UE。
5. UE成功接收到HOcmd后，HO执行阶段开始。这个阶段里UE通过小区同步和随机接入过程来切换到目标小区。
6. 当UE能够接收到T-BS的广播信息时，就发送一个HO确认信息（HO confirmation， HOconf）。
7. 在HO完成阶段，用户数据网关（user data gateway，UDGW）将DL数据路径切换到目标侧，之后T-BS开始从UDGW接收数据包。
8. 最终，T-BS给S-BS发送一个HO完成信息。之后S-BS释放所有之前分配给这个UE的资源。

### IDLE_MODE vs CONNECTED_MODE Mobility

与小区（再）选择不同，HO发生在用户处在进行数据会话或正在打电话的活动状态。当从一个BS移动到另一个，处在活动状态的用户改变它的服务小区。

### Handover Types

#### Inter-/Intra-Frequency Handover

如果服务和目标BS在同一个载波频率上运行，那么将他们称作频内邻居。UE会测量来自不同邻小区的下行RS，而且通常是网络传递信息告诉UE在给定的时间要测量哪些RS。

如果服务和目标BS在不同的载波频率上运行，那么将他们称作频间邻居。这时，UE需要测量间隙（measurement gap）来对不同频段进行测量。测量间隙是为了让UE能够转换到目标载波频率上并进行测量。

一个UE必须按BS所指示的优先级测量频内和频间信号质量。需要测量的频率越多，所要求的电池能耗越大，这也是挑战之一。另外一个未来的主要挑战是降低测量间隙。

#### Inter-/Intra-Cell Layer Handover
