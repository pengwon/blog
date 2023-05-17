---
title: esp32-micropython开发环境搭建
date: 2023-05-17 11:41:37
categories:
tags:
    - esp32
    - micropython
    - python
---

ESP32一般有两种开发方式，c语言开发和python开发：

- C语言开发使用的是乐鑫官方的sdk，这个sdk跑的是freeRTOS系统。

- python开发使用的是MicroPython，由第三方提供的支持。

两种开发各有优缺点：

- c语言编写的优点是运行的快，但是IDE使用起来比较混乱(有乐鑫自家Espressif-IDE或vscode+Espressif插件)，各种环境配置方法乐鑫来回变，教程极容易过时，同时c语言写的时候难度比较大。

- MicroPython需要提前向esp32烧录解析python的bin固件，编写简单，开发环境配置也简单，但运行速度慢，同时操作外设的时候，需要固件对外设进行支持，所以固件版本很重要。

对于我平时来说，大部分都是geek，使用ESP32都是用来使用蓝牙、WiFi、显示和一些简单的外设，很少用到实时性很高的控制、采集、传输，因此使用MicroPython简单快速，可以说性价比非常高。

<!-- more -->

## 1. 下载开发环境

本教程使用的是Thonny+MicroPython+ESP32的开发环境。

### 1.1 简介、下载、安装Thonny IDE

Thonny 是一款对初学者特别友好的轻量级开源IDE，不需要复杂的配置环境，支持三大操作系统(Windows、Mac、Linux）,支持简体中文，支持断点调试，它是由爱沙尼亚的 Tartu 大学开发，十分易于上手，还支持插件。

- Thonny 的git仓库: https://github.com/thonny/thonny

- Thonny 的下载地址：https://thonny.org/

下载方法有两种，一种是[官网下载](https://thonny.org/)，如下：

![下载thonny-1](https://imgs.boringhex.top/blog/1_下载thonny.png)

另一种方法是进入Thonny的[git仓库下载](https://github.com/thonny/thonny/releases/tag/v4.0.2)：

![下载thonny-2](https://imgs.boringhex.top/blog/2_下载thonny.png)

下载后，安装，安装完成后打开Thonny软件：

![打开thonny](https://imgs.boringhex.top/blog/3_打开thonny.png)

### 1.2 下载MicroPython

MicroPython是需要下载到ESP32中的python解释器的固件，用来解析将来下载到esp32中的py文件，并为py文件提供硬件的接口api函数，MicroPython文件很小，正因如此，esp32中的python只支持标准python的部分功能，因此当您在使用MicroPython对ESP32编程前，一定要查好这个固件是否支持。当然MicroPython几乎实现了esp32的全部，但若是特殊需求还是先查一下资料为好。

MicroPython由第三方提供，下载地址：https://micropython.org/download/esp32/

![下载micropython](https://imgs.boringhex.top/blog/4_下载micropython固件.png)

下载后是一个bin文件：如我下载后的文件名为`esp32-20220618-v1.19.1.bin`。

### 1.3 下载串口驱动

之所以下载串口驱动，是因为IDE和esp32通信，要借助一颗USB转串口的芯片，这种芯片型号众多，要根据您开发板上面的芯片型号来安装对应的驱动，常用的芯片有CP21XX，CH340等。本开发板搭载的是CH340，对于大多数电脑这个串口芯片是免驱的，对于其他芯片驱动，可以在网上搜索，自行下载。

安装完驱动后会在`设备管理器`显示串口的串口号

## 2.配置开发环境

通过上面的操作，下载和安装开发环境的操作就完成了，现在只需要配置一下，就可以用来开发EP32了。

### 2.1 将固件下载到esp32中

对于使用VSCode开发的小伙伴，下载固件是需要另一个软件来实现的，但是对于使用Thonny的人来说，配置将非常简单。

第一步：将开发板通过一条type-c的数据线，连接到电脑上面，打开电脑的的设备管理器，如下：

![下载esp32固件](https://imgs.boringhex.top/blog/5_下载固件到esp32.png)

若设备管理器中没有这个com，则表示驱动没有安装，[没安装驱动的请点此会看上面章节](#1.3 下载串口驱动)。

第二部：打开Thonny，点击`运行`->`配置解释器`，然后进行如下配置：

![配置解释器](https://imgs.boringhex.top/blog/202304282205972.png)

上图Port选择在设备管理器看到的那个esp32串口，Firmware选择上面下载的esp32固件，点击安装，就开始向esp32烧录固件了。

这时候在安装按钮的左边会显示一个安装的进度条，这个时间会有些长，稍作等待完成，当出现Error则表示安装失败，当显示Done表示下载完成。

### 2.2 配置Thonny终端连接到ESP32

还是在`运行`->`配置解释器`界面，选择端口如下：

![端口选择](https://imgs.boringhex.top/blog/202304282206054.png)

点击好的后，会在软件shell中显示如下内容，这个时候在shell中就可以输入python代码，他的运行环境是ESP32,如下：

![运行](https://imgs.boringhex.top/blog/202304282206186.png)

若没有出现shell界面，则需要设置一下，记得也将文件勾选上，如下：

![选项](https://imgs.boringhex.top/blog/202304282206462.png)

勾选上面文件的目的是要在软件中显示ESP中的文件，如下：

![显示中文设置](https://imgs.boringhex.top/blog/202304282207356.png)

## 3. 编写一个hello world

按下面操作：

![hello world](https://imgs.boringhex.top/blog/202304282207169.png)

好的，现在您已经运行完第一个程序了，就这么简单，从后面我们将讲述更多程序和调试的方法。
