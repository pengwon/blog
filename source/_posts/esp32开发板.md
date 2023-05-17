---
title: esp32开发板
date: 2023-05-17 12:09:00
categories:
tags:
    - esp32
    - 硬件
    - diy
---

## 1. 制作背景

前段时间，做项目又剩余了30余片的ESP32，感觉也没什么用了，放那里闲置很是可惜。

再加上以前项目买了一些1.54寸黑白墨水屏，200*200分辨率的，兼容微雪的例程。

所以我就在闲暇的时候做了一套ESP32+墨水屏的开发板，再写一些教程，这样就能把它卖了。

不掩饰，就是处理一些闲置物料。

## 2. 资料说明

1. 这批开发板我打算基于MicroPython开发，使用Thonny做IDE,在工作之余制作一整套的教程。

2. 我会将PCB开源，通过立创小伙伴可以轻松白嫖到这个PCB，链接我放到最后。

3. 对于那些连焊接都懒得弄的小伙伴，我打样了一些，我的定价不贵，主要是邮费太贵。纯手工焊接，介意勿拍。

<!-- more -->

## 3. 简单介绍

这个开发板使用的是立创EDA专业版进行设计；

- esp32选择的是esp32-wroom-32模组，本质上其实就是esp32-S，个人感觉安信可做的模组在信号上面比乐鑫模组更好一些；

- 屏幕选择的是1.54寸的黑白的墨水屏，200*200分辨率的，支持局部刷新，兼容微雪的例程；

- USB转串口选择的CH340C，大部分电脑都是免驱的，开发板集成了自动下载电路；

具体的设计如下：

![底板PCB](https://imgs.boringhex.top/blog/202305112332609.png)

![原理图](https://imgs.boringhex.top/blog/202305112332201.png)

实物图：

![ESP32+墨水屏实物1](https://imgs.boringhex.top/blog/202305112332962.png)

![ESP32+墨水屏实物2](https://imgs.boringhex.top/blog/202305112333246.png)

![ESP32+墨水屏实物3](https://imgs.boringhex.top/blog/202305112333585.png)

## 4. 链接

- 立创白嫖链接：https://oshwhub.com/zhang0819/e-paperesp32

- 立创EDA工程下载：
  
  链接: https://pan.baidu.com/s/1R79vGZewT5_YdicCGp5eHg?pwd=i86y 提取码: i86y 复制这段内容后打开百度网盘手机App，操作更方便哦 

- 原理图免费下载：https://download.csdn.net/download/Zhichao_Zhang/87725338。

- 懒人购买链接：咸鱼搜索“我是哈搭石”购买，不定期上架。

- 学习例程：本微信公众号会定期更新教程
