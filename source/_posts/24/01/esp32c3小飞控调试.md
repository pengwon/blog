---
title: esp32c3小飞控调试
date: 2024-01-02 10:53:43
categories:
tags:
    - esp32
---

ESP32C3小飞控板赶在国庆节前发出打样，假期后上班就收到了样板，但是迟迟没有动手调试，这两天终于抽出时间调试了，调试过程还算顺利，基本没有遇到什么大问题，下面记录一下调试过程。

## 原理图

![ESP-FC原理图](https://imgs.boringhex.top/blog/esp-fc_v1.0.png)

比计划中多加了一个大气压力传感器，用作测高。主要特性：

- 单串锂电池供电，最大工作电压5.5V
- 电源电压、电流感测
- 锂电池温度感测
- DCDC降压电源，效率90%以上
- 3轴加速度计，3轴陀螺仪
- 气压计，高度精度10cm
- 2路动力电机通道，支持电流感测
- 2路数字IO通道
- USB TypeC接口，调试和烧写程序
- 1路串口，可用于连接GPS

<!-- more -->

## PCB

![ESP-FC PCB](https://imgs.boringhex.top/blog/SMT2309281757_T.png)

板形主要是为了跟3d打印的机头座舱配合：

![电池和电路板安装座](gif)

这个安装座下面固定飞控电路板，上面装电池：

![电池安装](https://imgs.boringhex.top/blog/20231025214314.png)

![电路板安装](https://imgs.boringhex.top/blog/20231025220007.png)

电路板除了ESP32C3 WiFi模块和气压计都是打样直接贴好的，板子收到后焊ESP32C3模块出了一点问题，5片样板只有一片一次点亮。

![1/5一次点亮](https://imgs.boringhex.top/blog/fb9f725f943518b1dc70184f7cf8788.jpg)

还是大意了啊！是用加热台焊的，没有低温锡膏，就用的中温锡膏，加热台调到200℃，没点亮的板子主要的问题就是模块焊盘没有预先上锡。这个LGA焊盘较多，且很多GND焊盘，用加热台时会导致模块上的焊盘温度不够而不上锡，预先上好锡后，再次放加热台上面，焊锡熔化后再稍微压一下模块，就OK了。

## 调试

这两天把ADC、I2C、PWM、WiFi都单独跑通了，开始陆续整合，今天把动力电机的调试记录一下。

程序主要就是ADC和PWM的初始化，加上设置PWM占空比和获取ADC读数的函数，比较简单。将WiFi模块连接到局域网后，通过udp通信，由PC控制占空比，并获取ADC读数。

ESP32C3的PWM占空比分辨率还是很高的，5kHz频率下有13位，即8192，我在上位机程序中每0.1s发送一次数据，将占空比加100，加到4000后再每次减100。

<iframe src="//player.bilibili.com/player.html?aid=620044111&bvid=BV1F84y1R7Dw&cid=1311121514&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

终端窗口中左侧是ESP32C3的调试输出，显示udp通信源地址和收到的占空比数值；右侧是控制端程序输出，有3列，分别是占空比、电机通道1的ADC读数、电机通道2的ADC读数。

从视频中可以看到，单个电机在50%占空比下，静态推力在14g左右，ADC电流值还没校准，显示的是原始读数，精度为12位，即4096。

附上测试程序：

```python
import socket
import time
import struct

client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

server_ip = '192.168.1.79'
server_port = 3333
server_address = (server_ip, server_port)

while True:
    for i in range(80):
        if i > 40:
            pwm = 8000 - i * 100
        else:
            pwm = i * 100
        client_socket.sendto(pwm.to_bytes(4, "little") + pwm.to_bytes(4, "little"), server_address)
        data, _ = client_socket.recvfrom(1024)
        data1, data2 = struct.unpack("<ii", data)
        print(pwm, data1, data2)
        time.sleep(0.1)

client_socket.close()
```
