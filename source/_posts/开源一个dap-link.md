---
title: 开源一个dap-link
date: 2023-05-17 12:22:13
categories:
tags:
    - 嵌入式
    - 工具
    - diy
    - 硬件
---

## 1. 介绍

DAP-LINK协议是ARM推出的开源下载调试协议，基于这个协议设计的下载器可以下载调试ARM内核的MCU。

因此我设计了一个开源的DAP-LINK下载器，其原理和3d模型如下：

原理图：

![原理图](https://imgs.boringhex.top/blog/202305090127735.png)

<!-- more -->

pcb：

![PCB](https://imgs.boringhex.top/blog/202305090128743.png)

因为usb的数据线不是很长，而且数据速度对于usb2.0来讲也不是很快，因此我只做了等长，并没有进行差分布线。

3d效果图：

![3d](https://imgs.boringhex.top/blog/202305090128227.png)

usb口采用TYPE-B接口，因为我认为这样的接口更加稳定，不易损坏。

焊接实物图：

![实物](https://imgs.boringhex.top/blog/202305090128459.png)

该DAP-LINK下载器使用的mcu是AIR32F103，这款mcu性能强过STM32f103，它有更快的下载速度，使用AIR32F103设计DAP-link，合宙官方提供了最新的V2固件，这个固件下载程序非常快。我已经将这个pcb开源，开源地址在文章最后贴出。欢迎大家copy使用。

同时我也小批量做了些板子，对于需要到手用的小伙伴可以购买发货（东西不贵，但是邮费很贵）。

## 2. 说明

1. 因为有些时候我们调试需要复位或者重启mcu，因此我在link上面直接设计了一个开关，通过开关可以控制5v和3.3v的输出。
2. 为了增加驱动能力，我的LDO选择了1117，他最高可以输出1A的电流，尽可能能

## 3. 开源地址

立创开源地址：https://oshwhub.com/zhang0819/ji-yu-air103-di-dap-link

合宙AIR32f103官方教程链接：https://wiki.luatos.com/chips/air32f103/mcu.html
