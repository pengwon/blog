---
title: freeeeg32
date: 2023-04-21 14:41:54
categories:
tags:
  - 嵌入式
  - diy
---

最近做脑电采集的项目，搜集项目资料时找到了freeeeg32这个开源项目。

## [FreeEEG32](https://github.com/neuroidss/FreeEEG32-beta)

FreeEEG32是一款可堆叠的开源32通道24位Σ-Δ同时采样板，旨在满足需要高质量，价格合理的研究级设备的科学家和脑黑客的需求。我们已经开发了这种脑电图好几年了，因为我们正在努力为昂贵的专有脑电图技术创建明确的开源答案。

FreeEEG32将四个8通道AD7771 ADC与超低噪声（<测量值为0.22 μV）的源和基准电压源集成在一起，全部由功能强大的STM32H7 ARM Cortex-M7驱动，该电源还可以处理预处理。它足够灵敏，可以处理高质量的脑电图数据采集等等。

FreeEEG32在Affero-General Public License（AGPL）下可用。项目文件在我们的 GitHub 存储库中可用。

<!-- more -->

### 特性

- 四个AD7771模数转换器
  - 24 位、高达 128 kSPS 同步采样[^1] 
  - 每个 ADC 8 个通道
  - 高达 8 倍可编程增益放大
  - 107 kSPS 时动态范围为 32 dB
  - 用于诊断的板载 SAR（逐次逼近寄存器）ADC
- STM32H7ARM Cortex M-7 微控制器
  - 480MHz 处理器
  - 1027 DMIPS（每秒 Dhrystone 百万条指令）
  - 2 MB 闪存、1 MB 静态存储、16 kB 一级缓存
  - 用于深度学习等的双精度浮点单元 （FPU）
  - 铬艺术图形加速
  - 数十种通信协议
- 独立的 USB 端口隔离电源和逻辑，符合安全标准
- SD 卡插槽，用于数据收集或扩展程序存储
- 堆叠针座，可集成I2C，通过一块板堆叠输出
- 提供松散或预焊接的针座（与我们的定制耳机不兼容）
- （在制品）头戴式时可能支持基本运动跟踪的加速度计
 
[^1]: 为了满足 EEG 标准，FreeEEG32 的固件在同时运行所有 32 个通道时针对每个通道 512 SPS 进行了优化。

### 可堆叠多达 256 个通道

![FreeEEG32堆叠](https://www.crowdsupply.com/img/3a72/alpha1-5-stack_jpg_content-body-gallery-xl.jpg)

![FreeEEG32堆叠多通道采集](https://www.crowdsupply.com/img/10a7/stack-output_png_content-body-gallery-xl.jpg)


通过堆叠FreeEEG32板，可以实现64-256个通道或更多通道的同时流式传输，这使得FreeEEG32成为市场上最实惠的高密度EEG系统。每个板独立连接到主机，但可以流式传输到同一软件实例以进行大量数据收集。

### 比较

![](https://imgs.boringhex.top/blog/20230421153037.png)

### 软件与社区

我们是不断增长的开发人员社区的一部分，他们已经在使用我们的平台并展示了其可行性。这包括对各种平台的软件支持，如[Brains@Play](https://app.brainsatplay.com/)（以前的WebBCI），[OpenVibe](http://openvibe.inria.fr/)，[EDFbrowser](https://www.teuniz.net/edfbrowser/)和[Brainflow](https://brainflow.readthedocs.io/en/stable/)。

### 开源脑科学

FreeEEG32目标是通过将这些重要技术交到需要它们的学生、研究人员和客户手中，加速科学进步并为医学进步做出贡献。我们可以一起参与我们这个时代最重要的科学项目之一：“解决”大脑问题。然而，要做到这一点，我们必须在不牺牲质量的情况下降低开发成本和最终用户定价。

FreeEEG32是一个正在进行的科学项目的一部分，该项目旨在探测大脑中的工作记忆，并测量θ和γ与记忆编码的相关性。它也是探索群体超越冥想效果的研究的核心。这个项目致力于科学、发现以及任何为这些目标而努力的人。为了改善全球科学话语和围绕神经技术建立社区，这个项目是完全开源的。

### 通用信号采集，超越脑电图

该板针对 EEG 相关应用进行了高度优化，这意味着我们已采取一切可能的步骤来控制噪声并隔离电源以实现安全操作。也就是说，FreeEEG32也可以用作通用信号采集设备。如果您的信号分析需要 32 通道同时进行 24 位采样，那么 FreeEEG32 可以满足您的需求。示例可能包括：

- 非脑电图生物信号采集和成像：心电图、心电图、肌电图、FNIRS 等。
- 创建其他类型的高通道信号分析设备
- 用于工业过程控制和其他应用的环境传感
- 极低频和超低频无线电信号采集
- 智能家居监测和控制
- 环境功率和噪声检测

![FreeEEG32电路板图片](https://www.crowdsupply.com/img/c6bb/beta-top_jpg_md-xl.jpg)
