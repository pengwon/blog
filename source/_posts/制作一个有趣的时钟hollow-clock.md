---
title: 制作一个有趣的时钟hollow-clock
date: 2023-03-28 15:16:54
categories:
tags:
  - 嵌入式
  - diy
---

在搜集创意时钟项目时，发现一个有意思的项目——[Hollow Clock 4](https://www.instructables.com/Hollow-Clock-4/)。对，这已经是作者的第4代产品了，使用微型步进电机制作了一个中空时钟。完成后是这个样子：

![hollow clock 4](https://imgs.boringhex.top/blog/F77HI74LB537UH8.jpg "hollow clock 4")

有两个演示视频：

<iframe src="//player.bilibili.com/player.html?bvid=BV1Jm4y1r78w&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

另一个是木质版本的：

<iframe src="//player.bilibili.com/player.html?bvid=BV1ic411V76Z&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

真的很有创意。接下来进入制作过程。

## Hollow Clock 4

### 材料

![材料](https://imgs.boringhex.top/blog/F3UWT8TLAL338YE.jpg "材料")

- 8mm(直径) × 3mm(厚度) 强磁铁 × 3
- M2螺丝若干，不同长度都买一些，有盒装多规格的
- 28BYJ-48步进电机 × 1
- 28BYJ-48步进电机驱动板 × 1
- arduino nano × 1

### 步骤1 打印零件

木质版的可以使用CNC加工，我们以3D打印版来制作。

![3d打印零件](https://imgs.boringhex.top/blog/FWO2FB7LAL33912.jpg "3d打印零件")

如果自己有3d打印机，可以看下打印尺寸能否满足。没有打印机或尺寸大的零件可以选择嘉立创旗下三维猴。

3d打印零件模型和arduino程序已经打包放在百度网盘👇

链接: https://pan.baidu.com/s/1b0F8Ls6Kzt5sTOBcIKl_yA?pwd=mfr5 提取码: mfr5 复制这段内容后打开百度网盘手机App，操作更方便哦 

需要注意以下几点：

- 选择打印服务时注意核对模型尺寸。
- "case-side-hole.stl"和"case-rear-hole.stl"二者选其一即可，前者是为了把包括arduino nano和电机驱动板在内的所有电路都放在里边，如果要使用其他类型的控制器和驱动板，可以选择后者。
- "washer1.stl"和"washer2.stl"是可选的调节间隙的垫片。

### 步骤2 电路

![烙铁焊接](https://imgs.boringhex.top/blog/FM0HLNILAL3394P.jpg)

![连线](https://imgs.boringhex.top/blog/FIXI7XZLE60C003.jpg)

因为底座部分空间非常有限，又有两块板子要放，所以在上图可以看到其实是把驱动板的排针掰弯了，然后直接焊到arduino上。

要测试电路连接，可以把"stepper-test.ino"下载到arduino里。

### 步骤3 组装底座驱动单元

![组装底座](https://imgs.boringhex.top/blog/FEEA071LAL33A4N.jpg)

- 清除零件上的毛刺，这样旋转才能顺滑。
- 组装前要确认所有轴都可以正常旋转。
- 如果锥齿配合太松，通过"washer1.stl"和"washer2.stl"垫圈调整间隙。

### 步骤4 组装上部显示单元

![显示单元](https://imgs.boringhex.top/blog/FNOPC9XLAL33B34.jpg)

![磁吸定位时针](https://imgs.boringhex.top/blog/FGQK99HLAL33AH4.jpg)

- 同样需要去除零件上的毛刺
- 堆叠好全部旋转零件
- 用螺丝将时针和分针固定在一起，要能各自顺滑转动
- 把磁铁装入，并确认好方向，让时针能够跟驱动齿轮相吸对齐
- 确认各个旋转零件都能顺滑转动

### 完成

将顶部显示单元和底座驱动单元装在一起，给arduino下载"hollow4.ino"程序。如果这个程序不能正常工作，可以再试试"hollow-8phase.ino"这个程序。这两个程序驱动步进电机的时序不太一样，制作时可能因为种种原因导致时钟驱动机构摩擦力不一样，可以根据实际情况选择使用哪个程序。

![即将完成](https://imgs.boringhex.top/blog/FISR9EELAL338XC.jpg)

![完成](https://imgs.boringhex.top/blog/FM9VWECLAL338XE.jpg)

### 可选

"hour-hand-5mm-shorter.stl"优化了时针的区分度，可以更明显的分辨时针和分针。因为时针更短了，所以磁铁吸力相应变弱，但正常情况仍然可以对齐。

![优化时针区分度](https://imgs.boringhex.top/blog/FABTCOBLAWIK8TN.jpg)

### Troubleshooting

- 如果锥齿太松，用垫片调整间隙。
- 如果方轴太松，用"bevel-gear-small-hole.stl"，这个是0间隙版本。或者垫些东西。
- 有评论反馈说下载程序后电机旋转方向是反的，尝试以下修改：
  
  ``` arduino
  int port[4] = {5, 4, 3, 2};
  ```
  
  改为

  ``` arduino
  int port[4] = {2, 3, 4, 5};
  ```

- 如果运行不稳定，可以先检查下电源。
- 时钟过快或过慢，可以调整`MILLIS_PER_MIN`的值，理论上应该是60000，越小的值转动越快。

### One More Thing

下一步将以这个为基础进行一轮改进。
