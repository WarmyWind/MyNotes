# A Survey on Handover Management: From LTE to NR

本文为关于从LTE到NR在基本HO场景下的主要不同的综述。

## Introduction

5G系统主要通过减少BS的覆盖范围、增加BS的密度来增加系统容量。这么做的坏处在于会增加HO，并且会增加信令开销，进而干扰数据流、降低用户的吞吐量。因此除了要提供高速率，提供可靠的HO机制对于保证QoE也同样重要。

小基站的低功耗使得他们能被部署在任何地方，并且根据节能的需要可以协调他们的开启与关闭。随着小基站的开启与关闭，移动用户的信道状况和邻小区列表会改变的非常快，这可能会导致干扰增加、能耗增加、HO过于频繁、乒乓效应和延迟增大。如果用户经历多个HO，HO延迟的影响会积累，最终导致用户体验的严重退化。

总的来说，我们可以将无线接入移动性分为两种过程：小区（再）选择和HO。小区（再）选择发生在UE处于空闲模式时，HO发生在UE处于连接模式时。本文主要关注HO。

> 1G时代的小区覆盖范围为数十公里，在1978年测试了1G的HO过程，包括了UE辅助程序紧接着由网络决策的过程。2G网络的小区大小减小，加重了HO问题。为了提高HO过程的速度，采取了信令开销很大的分布式HO决策，而乒乓效应通过加入一个滞后值（hysteresis）来处理。3G CDMA网络采取了软HO算法。这种软HO算法遵从make-before-break的理念，提高了增益但对其他用户造成了更大干扰，最终干扰超过增益。所以对于4G和5G技术，HO过程重新回到break-before-make方案。更小的小区带来了更小的HO区域，并且没有时间传输过多的HO信令，这要求HO过程尽量简明。

为了实现较高的移动数据流量，异构网（HetNets）被认为是最有前景的解决方法。HetNets有一些显著的挑战，比如小区间干扰协调（inter-cell-interference-coordination，ICIC）、移动性管理和回程配给（backhaul provisioning）。其中移动性管理格外重要。在异构部署中，UE都用相同的HO参数（比如TTT和hysteresis margin（HM））。但是，用相同的一套参数会降低移动性性能，因此需要自适应的管理方法来提高移动性性能。

### 5G Targets and the Importance od Mobility

5G的目标不仅局限于更高的系统容量、更高的数据率和更广的覆盖范围，还包括比如IoT设备要求的低耗、URLLC等。所以，具有前向兼容性的移动性管理是实现平稳的未来优化的重要设计原则。

### Motivation from an Energy Saving Point of View

电信排放的二氧化碳占总排放量的40%，设计有效的HO过程是减少能耗的一步。

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

与小区（再）选择不同，HO发生在用户处在进行数据会话或正在打电话的活动状态。当从一个BS移动到另一个BS，处在活动状态的用户改变它的服务小区叫做HO。

### Handover Types

#### Inter-/Intra-Frequency Handover

如果服务和目标BS在同一个载波频率上运行，那么将他们称作频内邻居。UE会测量来自不同邻小区的下行RS，而且通常是网络传递信息告诉UE在给定的时间要测量哪些RS。

如果服务和目标BS在不同的载波频率上运行，那么将他们称作频间邻居。这时，UE需要测量间隙（measurement gap）来对不同频段进行测量。测量间隙是为了让UE能够转换到目标载波频率上并进行测量。

一个UE必须按BS所指示的优先级测量频内和频间信号质量。需要测量的频率越多，所要求的电池能耗越大，这是挑战之一。未来的另外一个主要挑战是降低测量间隙。

#### Inter-/Intra-Cell Layer Handover

异构网包括许多层蜂窝网络（宏小区、微小区和微微小区等）。频内的宏-宏（微-微）切换被称为层内切换。

当微小区质量好于一定阈值时，UE切换到更小的小区层。这就被称为层间切换。层间切换除了追求更好的信道，另一个理由是负载均衡。层内切换既可以是频内的也可以是频间的。频内-层间切换的主要挑战是来自宏基站和微基站两者的干扰，频间-层间切换的主要挑战是UE由于需要测量其他频率所带来的高能耗。

在异构网中的层间切换的未来研究方向大致为三点：

1. 相比于层内切换，更频繁的层间切换导致的乒乓效应和信令开销。
2. 能耗策略
3. 资源分配、干扰消除、功率控制、负载均衡等联合优化

#### Inter-/Intra-RAT Handover

横向的HO发生在相同的无线电接入技术（radio access technology，RAT）中，也被叫做RAT内切换。发生在不同的RAT之间的切换被称作RAT间切换，如UE从3G网络切换到2G网络。

#### Inter-/Intra-Operator Handover

在切换到不同的运营商网络被称作运营商间切换，一个典型的例子是漫游。

### Measurements and Reporting

在LTE中，参考信号接收功率（RSRP）被定义为没有干扰和噪声时的平均接收功率，信号强度指标（RSSI）则是包括了干扰和噪声的总接收功率，而参考信号接收质量（RSRQ）被定义为RSRP与RSSI的比值。当RSRP的测量值低于某个阈值，那么UE开始测量邻基站。为了便于从相邻小区进行测量，UE在测量间隙的一段持续时间内停止DL数据传输。

LTE系统中，滤波（包括L1和L3层滤波）后得到T-BS的SS比服务小区高于一定值时（这个值被称作A3 offset），就满足了A3事件的进入条件，A3需要在TTT时间内保持满足进入条件（否则就会退出）。针对HO的优化可以调整TTT、A3 offset和L3滤波器系数K。

总的来说，报告准则可以分为三种：

1. 事件触发报告：UE在特定的事件发生后报告测量。
2. 周期性报告：UE在特定的时间间隔报告测量。
3. 按需/盲报告：UE在收到BS的请求后立刻报告测量。该种报告是用于支持负载均衡的。

### Break-Before-Make vs Make-Before-Break

**硬HO**指的是UE一次只连接一个BS，这就会导致在与T-BS建立新的连接之前，与S-BS的连接会先中断，这种HO也被称为**break-before-make HO**。与之相对的，**软HO**发生在UE可以一段时间内同时连接到两个BS，从而减少甚至消除在HO过程中连接中断的时间，这种HO有时被称为**make-before-break(MBB) HO**。

> 在LTE系统的break-before-make HO每次都有40ms的连接中断。而在5G NR系统中，零HO执行（HO execution，HOE）时间、低延迟、高可靠、道路车辆安全和高效是连接模式移动性的用例。

5G NR中，HOE时间可以用MBB策略，以及多校区连接和同步HO来减少。但要注意的是，由于毫米波信号会被建筑物、砂浆、砖块和人体阻挡，所以在单独的NR体系中，传统HO方法的反应不够快速。一种解决的方案是多连接。

### Handover Performance Metrics

- HOF率
- HO频率：指每秒HO的次数
- PP率：一段时间内乒乓时间的发生次数
- HO能耗
- HO成功率
- 数据延迟：从S-BS接受最后一个数据包到从T-BS接收第一个数据包之间的时间
- HO中断时间：在HO过程中UE不能和任何BS交换用户面数据包的时间

## Handover Management in LTE

### Key Features of LTE Handover Management

3GPP LTE定义的HO过程可以用4步表示：

1. 服务和邻eNB传输DL RS
2. UE执行SS测量
3. UE给服务eNB发送MR
4. 基于收到的MR，服务eNB决定HO并向目标eNB发送HO请求

#### Handover Failures

总的来说，HO失败可以由错误的HO决定来分类：

- 过早HO：在HO后，UE连接回原来的服务小区（导致乒乓效应）
- 过晚HO：在切换到目标小区完成之前，服务小区的信道质量降得过低
- 切换到了错误的小区：UE从目标小区断开并连接到了一个新小区

#### Literature Survey

略。

## Handover Management in NR

5G NR的关键特征包括：高频段、频谱灵活性、前向兼容性和极简设计。
