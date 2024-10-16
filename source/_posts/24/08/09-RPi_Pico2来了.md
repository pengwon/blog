---
title: 树莓派Pico 2来了
date: 2024-08-09 11:24:53
categories:
tags:
    - 嵌入式
---


这两天开源圈的大事之一，就是树莓派基金会发布了树莓派Pico 2。

帖子原文：[Raspberry Pi Pico 2, our new $5 microcontroller board, on sale now](https://www.raspberrypi.com/news/raspberry-pi-pico-2-our-new-5-microcontroller-board-on-sale-now/)

![Pico 2](https://imgs.boringhex.top/blog/20240809121833.png)

总结一些关键信息：

- **产品发布**：Raspberry Pi Pico 2 是 Raspberry Pi 基金会推出的第二代微控制器板。
- **芯片设计**：该板基于 RP2350 芯片，这是一款在 Raspberry Pi 设计的高性能、安全的微控制器。
- **性能提升**：Pico 2 提供了更高的核心时钟速度、两倍的内存、更强大的 Arm 核心。
- **安全特性**：增加了新的安全功能。
- **接口能力**：升级了接口能力，提供了更好的性能和功能。
- **兼容性**：保持了与 Pico 系列早期产品的硬件和软件兼容性。
- **售价**：Pico 2 的售价为 5 美元。

这款新产品的发布对于电子爱好者和开发者来说是个好消息，因为它提供了更高的性能和更多的功能，同时保持了低成本和良好的兼容性。这使得 Pico 2 成为各种项目的一个有吸引力的选择，从教育用途到复杂的电子设计。

<!-- more -->

## RP2040：工程师的微控制器

RP2040微控制器自推出以来，凭借其高性能和多功能性，在全球范围内取得了巨大成功，销量近四百万台。这款微控制器具备双32位核心、丰富的片上RAM和灵活的接口，为电子工程师和爱好者提供了强大的开发平台。尽管如此，Raspberry Pi团队并未止步，他们倾听用户反馈，不断追求改进，计划在下一代产品中加入更多功能，如片上存储、低功耗模式、更快的核心和更多的内存，以满足更广泛的应用需求。

## RP2350

两年前，随着为 Raspberry Pi 5 设计的 RP1 I/O 控制器的完成，Raspberry Pi 芯片团队开始研发后来成为 RP2350 的产品。RP2350 是一款比 RP2040 复杂得多的设计，其特点包括：

- 两个 150MHz 的 Arm Cortex-M33 核心，支持浮点数和 DSP
- 520KB 的片上 SRAM，分为十个可同时访问的银行
- 以 Arm TrustZone for Cortex-M 为核心的全面安全架构，包括：
  - 签名启动支持
  - 8KB 的片上反熔丝一次性可编程（OTP）内存
  - SHA-256 加速
  - 硬件真随机数生成器（TRNG）
- 片上开关电源和低静态电流 LDO
- 十二个升级的 PIO 状态机
- 新的 HSTX 外设，用于高速数据传输
- 支持外部 QSPI PSRAM

与 RP2040 只提供一个 7×7mm，QFN56 封装选项不同，这次我们提供了选择：一个 7×7mm，QFN60 封装（RP2350A）带有 30 个 GPIO，或一个 10×10mm，QFN80 封装（RP2350B）带有 48 个 GPIO；以及每种封装的变体，带有 2MB 的堆叠式 QSPI 闪存（RP2354A 和 RP2354B）。

价格仍然保持实惠：尽管RP2350的硅晶圆现在尺寸为奢侈的 5.3mm²，而 RP2040 为 2mm²，但 RP2350A 的价格只比 RP2040 贵十美分，以 3400 单位卷的价格为 0.80 美元，单个单位的价格为 1.10 美元。RP2350B 的价格比 RP2350A 贵十美分，而 RP2354 变体的价格比它们的无闪存兄弟贵二十美分。

RP2350 将在 2024 年底前大量供货。

Pico 2 配对 RP2350A 和 4MB 的外部 QSPI 闪存，比原始 Pico 的 2MB 有所增加。它在形式因素和电气方面与原始 Pico 设计兼容。

### 软件和文档

Raspberry Pi 不仅仅是一家硬件公司，也是一家软件公司：开发者通过我们的软件平台体验我们的硬件产品，我们在完善这些平台方面投入了大量的时间和金钱。

如您所料，Pico 2 和 RP2350 的推出伴随着 Pico SDK 的更新版本，以及新的 MicroPython 和 CircuitPython 映像。Jonathan Pallant 和他的同事们一直在努力将 Rust 语言引入我们的新平台。

我们与 Trusted Firmware 项目合作，将 RP2350 定位为 Trusted Firmware-M 2.1.0 长期支持版本的参考硬件平台。TF-M 为 Arm v8-M 芯片上的 PSA Certified 提供参考实现，为开发者提供了一条简单的路径，以防止设备遭受常见攻击。RP2350 将由认证的独立实验室进行实验室测试，目标是赶在 10 月发布前达到 PSA Certified Level 2。

最后，我们很高兴与 Google 合作推出支持 Pico 2 的 Pigweed SDK。Pigweed 的中间件库已经在数百万台设备上发货，包括 Google 自己的 Pixel 设备和 Nest 温控器。现在，Pigweed SDK 让您在为 Pico 2 构建项目时更容易使用这些组件。请访问 Google 的公告页面了解更多信息！

我们的安全模型基石是签名启动。如果启用安全功能，只有使用私钥签名的二进制文件才能启动，其对应的公钥哈希存储在 OTP 中。阻止攻击者运行任意代码大大增加了提取 OTP 内容（包括用于代码保护的加密密钥）的任务难度。

其他厂商在实现启动安全方面的记录相当糟糕。广泛市场的微控制器通常缺乏针对现代故障注入攻击的有效对策，例如 LimitedResults 对 Nordic nRF52 设备的 Debug Resurrection 攻击和 Chris Gerlinsky 绕过 NXP LPC 系列设备的代码读取保护的工作。

虽然 RP2350 使用了包括硬件快速毛刺检测器和我们专利申请中的冗余协处理器在内的多种技术来保护控制流和数据完整性免受故障注入攻击，但我们完全预期在启动过程中发现并修复缺陷。我们希望在 RP2350 部署到关键应用之前找到这些缺陷。

在发布前，我们委托 NewAE 和 Hextree 审核我们的安全架构。现在，我们为第一个确认破解我们签名启动过程的提供 10,000 美元的奖金。这将首次运行一个月：如果在此期间没有发现缺陷，

[Pico 2产品页](https://www.raspberrypi.com/products/raspberry-pi-pico-2/)

![Pico 2](https://imgs.boringhex.top/blog/20240809134720.png)

Raspberry Pi Pico 2 系列包括两款板子：Raspberry Pi Pico 2 和带有插座的 Raspberry Pi Pico 2。

## Raspberry Pi Pico 2

Raspberry Pi Pico 2 是一款低成本、高性能的微控制器板，具有灵活的数字接口。其主要特点包括：

- 由英国 Raspberry Pi 设计的 RP2350 微控制器芯片
- 双 Cortex-M33 或 Hazard3 处理器，最高频率可达 150MHz
- 520KB 的 SRAM 和 4MB 的板载闪存
- USB 1.1，支持设备和主机模式
- 低功耗睡眠和休眠模式
- 通过 USB 大容量存储进行拖放编程
- 26× 多功能 GPIO 引脚，其中 3 个可用于 ADC
- 2× SPI, 2× I2C, 2× UART, 3× 12 位 500ksps 模数转换器（ADC），24× 可控 PWM 通道
- 2× 定时器，带 4 个警报，1× AON 定时器
- 温度传感器
- 3 × 可编程 IO (PIO) 块，总共 12 个状态机，用于自定义外设支持
- 灵活、用户可编程的高速 IO
- 可以模拟 SD 卡和 VGA 等接口

Raspberry Pi Pico 2 以模块形式提供，允许直接焊接至载板，而带有插座的 Pico 2 则带有预先焊接的插座。

> 注意
> 两款板子都设有三针串行线调试（SWD）插座。然而，带有插座的 Pico 2 将其引出至一个小型的、带键的 3 针连接器，而 Pico 则有三个城堡形的通孔引脚，位于板子边缘附近。

### 引脚图和设计文件

![引脚图](https://imgs.boringhex.top/blog/20240809140203.png)

- 下载[引脚图（PDF）](https://datasheets.raspberrypi.com/pico/Pico-2-Pinout.pdf)
- 下载[设计文件（Cadence Allegro）](https://datasheets.raspberrypi.com/pico/RPi-Pico-2-PUBLIC-20240708.zip)
- 下载 [STEP 文件](https://datasheets.raspberrypi.com/pico/Pico-2-step-20240708.zip)
- 下载适用于 Raspberry Pi Pico 的 [Fritzing 零件](https://datasheets.raspberrypi.com/pico/Pico-2-Fritzing-20240708.fzpz)


确实很强！看来这次必须跟进一下了。

后台回复 `Pico2`，可以获取Pico2设计文件（官网说是Cadence格式，下载下来是KiCad，而且是参考设计，并不是Pico2源文件，应该是网站的Bug，不影响参考学习）。