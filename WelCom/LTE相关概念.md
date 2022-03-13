# 4G LTE/5G NR相关基础概念

1. FR：Frequency Range。3GPP为5G定义了两个FR：
   - FR1：450MHz~6000MHz
   - FR2：24250MHz~52600MHz
     4G LTE只能使用FR1。
2. OFDM参数集：OFDM Numerology（也即Configuration）
   - 子载波间隔SCS：SubcarrierSpace，通常使用序号$\mu\in\{0,1,2,3,4\}$来表示5G NR中的5种不同SCS，对应的$\Delta f$分别为15kHz，30kHz，60kHz，120kHz和240kHz。而4G LTE则仅支持15kHz。
   - 5G NR对所有SCS都支持普通循环前缀，但仅对$\mu=2$（即$\Delta f=60kHz$）支持拓展循环前缀。SCS从15kHz到120kHz适用于共享信道，而240kHz用于同步信号。具体的SCS取值还与具体的频段有关，比如对于28GHz和39GHz毫米波频段，可用的SCS为60kHz和120kHz。
3. 有用符号时间（长度）：OFDM的符号时间长度$T_u$与SCS呈反比：$T_u=1/\Delta f$。由于5G NR有5中不同的SCS，因此有对应的5种符号时间：$T_u,0.5T_u,0.25T_u,0.125T_u,0.0625T_u$，其中$T_u=66.67\mu s$。
4. 帧：frame，4G LTE和5G NR都规定了一帧为10ms，每一帧都被划分为了长度为1ms的子帧（subframe）。
5. 时隙：slot，在5G NR中被视作可调度的最小单元。在4G LTE系统中1个子帧被固定划分为了2个0.5ms的时隙。在5G NR中时隙大小是根据$T_u$决定的：使用普通循环前缀时，一个时隙有14个OFDM符号，使用拓展循环前缀时则有12个PFDM符号。
6. RE：Resource Element，频域1个子载波，时域1个符号。
7. RB：Resource Block，5G NR仅规定一个RB包含12个子载波（根据numerology不同，RB的带宽也不同），对RB的时间长度并没有定义。4G LTE不仅规定了一个RB包含12个子载波（12x15=180kHz），还规定了时间长度为7个普通循环前缀OFDM符号或6个拓展循环前缀OFDM符号。
8. MCS：Modulation and Coding Scheme，5G NR中通常使用序号0~31表示32种不同方式。MCS决定了一个符号能够传输的比特数和传输码率，其中传输码率（code rate）是指有用比特数与总比特数的比值。通常MCS的选择与信道质量（一般用SINR评估）紧密联系，但具体的MCS选择要通过一系列复杂的算法完成。
