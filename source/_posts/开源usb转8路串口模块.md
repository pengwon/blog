---
title: 开源usb转8路串口模块
date: 2023-05-17 13:54:39
categories:
tags:
    - 硬件
    - 工具
---

今天给大家分享一款USB转8路串口的PCB设计，并提供一种跨电脑跨USB口的端口号自动识别方法。

## 1. 简介

芯片采用沁恒的CH348Q，不需要写程序，其中4路串口支持流控，另外的4路不支持。

开发板设计如下：

原理图：

![USB转8路串口原理图](https://imgs.boringhex.top/blog/202305132323837.png "原理图")

<!-- more -->

pcb图：

![USB转8路串口PCB图](https://imgs.boringhex.top/blog/202305132326400.png "PCB图")

3d图：

![USB转8路串口3D图](https://imgs.boringhex.top/blog/202305132328830.png "3D图")

## 2. 说明

* 因为我平时就是用这个工具调程序，因此我usb接口使用的TYPE-B接口，这样的接口更耐用也跟那个稳定。
* 开发板每路串口都带有TX、RX指示灯，这样在调试的时候就会非常方便。
* 开发板另外还有两颗指示灯，一个是power，用来指示设备上电，另一个是ACR，用来指示CH348q可以正常工作，CH348q官网只提供了WIN上面的驱动，当驱动未安装，ACR不会亮，表明CH348q不可以正常工作，当驱动安装好，acr会亮起，表示设备可以正常工作。

产品实物图如下：

![USB转8路串口实物图](https://imgs.boringhex.top/blog/202305132333587.png "USB转8路串口实物图")

![USB转8路串口实物图](https://imgs.boringhex.top/blog/202305132334165.png "USB转8路串口实物图")

## 3. 一种跨电脑跨USB口的端口号自动识别方法

在平时我们使用的usb转串口模块，按照更换usb口串口号是否会改变分为usb口改变设备和usb口不改变设备(其实根本原因是支持)。

比如我们常使用的ch340，它就是一款更换usb口后，会重新生成COM口编号的设备，这种设备的优点是对于大部分PC是免驱的, 缺点是当电脑同时接入多个串口，需要挨个插拔来确定端口号。

比如我们常用的cp21xx，它就是一款更换usb口后，依旧会沿用上一次端口号的设备，这种设备的优点是在更换usb口后不需要在从新确认端口号，方便了，缺点是需要驱动，因此在linux上使用的时候，一定要确保有驱动。

但是不管上面的哪一种，当跨PC来接入的时候，谁也保证不了COM不会发生改变。这样其实就有一个问题，当我们写了一个上位机程序的时候，我们必须留出一个可以设置COM口的地方，因为当存在多个COM的时候，程序无法自动识别，需要用户指定,那这样对于那些自动化测试、无人值守项目就会很崩溃。下面我们讲解使用CH348完美解决这个问题的方法。

我们先分析一下实现的原理，我们将设备插入电脑，然后打开的[猫猫串口助手(推荐使用的一个串口助手)，下载链接](http://www.amaocom.com/)，当点击串口号的时候，在输出测输出了这8个串口，如下：

![猫猫助手显示](https://imgs.boringhex.top/blog/202305132335811.png "猫猫助手显示")

我们会发现这8个串口都有对应的名字，如WCH USB-SERIAL Ch B (COM26)中，COM26就是com口，前面那一段就是串口的名字，巧合的是在不同的电脑上，com口可能不同，但是名字是相同的，如：这WCH USB-SERIAL Ch A、B、C、D、E、F、G、H对应的就是8个串口的名字，这些名字和开发板上面的串口一一对应，正如我画的pcb那样，我画的PCB，不是写的串口1、串口2，而是以ABCDEFGH来定义串口的，现在我们使用python来自动找到A串口对应的端口号：

```python
# 通过串口的名称找到对应的串口，并打开
# pip install pyserial

import serial
import serial.tools.list_ports


# 举例：比如我们要查找WCH USB-SERIAL Ch A对应的端口号
my_serial_1 = "WCH USB-SERIAL Ch A"

if __name__ == "__main__":
    pass
    # 判断存在用户输入的串口
    ports = list(serial.tools.list_ports.comports(include_links=False))
    # 打印所有的port
    if not ports:
        print("err: no port")
    else:
        print("PC link all port:")
        for port in ports:
            print(port.description)
        print("------------------")
        # 连接用户指定的串口
        for port in ports: 
            if port.description.find(my_serial_1) != -1:
                print(my_serial_1 + " find " + port.name)
                this_serial = serial.Serial(port.name, 9600, timeout = 0.5) 
                break
        else:
            # 用户输入的端口不存在
            print("err: %s port does not exist." %my_serial_1)
```

上面这段程序是寻找"WCH USB-SERIAL Ch A"对应的com口，我们会发现port里面有串口名称信息，我们只需要用python的find判断就可以找到com了，如下：

![自动搜索串口](https://imgs.boringhex.top/blog/202305132336833.png "自动搜索串口")

我们更换了电脑后，com号有可能会改变，但是串口的名称不会改变，也就是我们时刻知道A和CH348的那个串口是对应的，我们通过A找到对应的串口，然后打开，这样就能自动识别每一路串口了，这对自动化来说非常有用。

当然这个也是有缺点的，当用户同时插入两个，就会出现两个"WCH USB-SERIAL Ch A"，这种情况就要借助沁恒加自带的工具，来修改开发板的信息了，这种情况就不再深入讨论，毕竟8个串口已经足够多了。

## 4.开源地址

立创开源链接：https://oshwhub.com/zhang0819/ch348q_usb_uartx8

懒人链接：这个板子我也打了一些样品，咸鱼搜索“我是哈搭石”购买。
