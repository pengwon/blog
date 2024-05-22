---
title: esp32c3-vscode开发环境搭建
date: 2023-06-19 12:25:59
categories:
tags:
    - esp32
    - 嵌入式
---

之前的文章 [ESP32+MicroPython开发环境的搭建](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481010&idx=1&sn=e7d542adb3eef1b9b4067392d1a0f177&chksm=84ad7571b3dafc679c88ae5972ad0591644dc47ffeabd7ea8b9be5db691763c0bce002e4f25e#rd) 介绍了ESP32 MicroPython开发环境的搭建，此次计划采用ESP32-C3做小飞机主控，不太适合用MicroPython，要用ESP-IDF原生框架，因为平时一直用vs code编辑器，所以就选了vs code做开发环境。

## ESP32-C3

ESP32-C3 SoC 芯片支持以下功能：

- 2.4 GHz Wi-Fi
- 低功耗蓝牙
- 高性能 32 位 RISC-V 单核处理器
- 多种外设
- 内置安全硬件

ESP32-C3 采用 40 nm 工艺制成，具有最佳的功耗性能、射频性能、稳定性、通用性和可靠性，适用于各种应用场景和不同功耗需求。

<!-- more -->

乐鑫为用户提供完整的软、硬件资源，进行 ESP32-C3 硬件设备的开发。其中，乐鑫的软件开发环境 ESP-IDF 旨在协助用户快速开发物联网 (IoT) 应用，可满足用户对 Wi-Fi、蓝牙、低功耗等方面的要求。

![ESP32-C3系列芯片命名和对比](https://imgs.boringhex.top/blog/20230616144720.png)

## 开发环境

### 硬件

- ESP32-C3开发板
- USB数据线
- PC

### 软件

- VS Code

#### 安装插件

1. 在vs code插件市场搜“esp”之类的关键字：

   ![安装esp插件](https://imgs.boringhex.top/blog/20230616145720.png)

2. 安装插件后，在vs code命令面板输入“esp-idf”（额，好多命令呀！）：
   
   ![配置插件](https://imgs.boringhex.top/blog/20230616150902.png)

   ![EXPRESS快速安装](https://imgs.boringhex.top/blog/20230616151058.png)

   按照默认选项直接安装就行，idf版本我选的master开发分支，生产环境可以选正式release的稳定版。如果遇到连接不上GitHub的问题，可以把下载服务器切换为乐鑫服务器试试。

   ![配置完成](https://imgs.boringhex.top/blog/20230616153059.png)

   看到这个，就说明配置OK了。

#### blink示例

1. 新建一个`esp-test`文件夹，然后在其中打开vs code。
2. 在vs code命令面板中输入“esp show”
   
   ![打开示例](https://imgs.boringhex.top/blog/20230616155126.png)

3. 选择blink示例
4. 点击左下角`build`按钮进行构建
   
   ![构建](https://imgs.boringhex.top/blog/20230616160920.png)

   首次构建非常耗时，会把整个框架都编译，以后再构建就不会这么长时间了。

总的来说，乐鑫这一套工具已经非常完善了，同时也很复杂，看来需要花时间慢慢熟悉这个开发流程了。
