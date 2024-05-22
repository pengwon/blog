---
title: esp32点亮led
date: 2023-05-17 12:04:04
categories:
tags:
    - esp32
    - micropython
    - python
---

对于应用程序，我们第一个程序是打印。对于嵌入式，我们第一个程序是点灯。

## 1. 连接开发板

将开发板链接到电脑，打开Thonny，配置串口，如下：

![配置串口](https://imgs.boringhex.top/blog/202305071003017.png)

<!-- more -->

然后就会发现IED的shell显示已经连接到esp32解释器，如下：

![连接esp32](https://imgs.boringhex.top/blog/202305071004081.png)

这时候IDE已经串口连接到开发板了，若没有连接，请自行翻看环境配置章节内容。

## 2. micropython关于GPIO的描述

led的控制，是通过将GPIO配置成输出模式，然后控制输出电平的高低来实现的。

对于任何嵌入式开发，数据手册和demo是必不可少的，对于Micropython开发，我们必看的是MicroPython官方的文档[GPIO章节](https://docs.micropython.org/en/latest/esp32/quickref.html#pins-and-gpio)。如下：

![配置gpio](https://imgs.boringhex.top/blog/202305071005000.png)

大概意思是说GPIO的使用是通过machine.Pin类来实现的：

```python
from machine import Pin

p0 = Pin(0, Pin.OUT)    # create output pin on GPIO0
p0.on()                 # set pin to "on" (high) level
p0.off()                # set pin to "off" (low) level
p0.value(1)             # set pin to on/high

p2 = Pin(2, Pin.IN)     # create input pin on GPIO2
print(p2.value())       # get value, 0 or 1

p4 = Pin(4, Pin.IN, Pin.PULL_UP) # enable internal pull-up resistor
p5 = Pin(5, Pin.OUT, value=1) # set pin high on creation
p6 = Pin(6, Pin.OUT, drive=Pin.DRIVE_3) # set maximum drive strength
```

引脚范围（含）：0-19、21-23、25-27、32-39。这些对应ESP32芯片实际的GPIO管脚号。注意许多最终用户板使用他们自己的临时引脚编号（例如标记为 D0、D1 等）。有关电路板逻辑引脚和物理芯片引脚之间的映射，请参阅您的电路板文档。

支持四种驱动强度，引脚使用`Pin()`这个构造函数或`Pin.init()`方法，通过`drive`的关键字参数 ，可以设置不同的对应最大安全源/灌电流和近似内部驱动器电阻：

> - `Pin.DRIVE_0`：5毫安/ 130欧姆
> - `Pin.DRIVE_1`：10毫安/ 60欧姆
> - `Pin.DRIVE_2`：20mA / 30 欧姆（如果未配置则为默认强度）
> - `Pin.DRIVE_3`：40毫安/ 15欧姆

Pin()和Pin.init()的hold关键字参数将启用ESP32的“pad hold”功能。当设置为True时，引脚配置(方向，拉电阻和输出值)将保持不变，任何进一步的改变(包括改变输出电平)将不应用。设置hold=False将立即应用任何未完成的引脚配置更改并释放引脚。当一个引脚已经保持时，使用hold=True将应用任何配置更改，然后立即重新应用hold。

笔记：

- Pin 1 和 3 分别是 REPL UART TX 和 RX
- Pins 6, 7, 8, 11, 16, and 17 用于连接嵌入式flash，不建议用于其他用途
- 引脚 34-39 仅供输入，也没有内部上拉电阻
- 有关睡眠期间管脚行为的讨论，请参见[深度睡眠模式](https://docs.micropython.org/en/latest/esp32/quickref.html#deep-sleep-mode)

有一个更高级别的抽象 [machine.Signal](https://docs.micropython.org/en/latest/library/machine.Signal.html#machine-signal) 。可用于反转引脚的信号。

## 3. 控制led的亮灭

下面我们看一下我们的原理图，首先看一下我们关于LED的原理图：

![led原理图](https://imgs.boringhex.top/blog/202305071005512.png)

我们可知，控制led的引脚为IO2，当引脚输出高电平，led亮，输出低电平，led灭。

我们现在ide的shell里面控制led，在shell界面里面按行输入下面内容，每输入一行点击一次回车，当出现>>>的时候输入下一行，如下：

![命令行控制led](https://imgs.boringhex.top/blog/202305071006144.png)

当输入`pin.on()`时候，观察led会亮起，当输入`pin.off()`led会熄灭。

> 注意:
>
> on和off控制的是GPIO输出高低，只是凑巧本开发板是共阴极的，因此on的时候引脚输出高，正好led亮。
>
> 对于共阳极的led，on是熄灭，off是亮起。

上面我们是在控制台打印的，想让esp32上电就运行程序需要将程序下载到esp32中，方法如下，点击`boot.py`,在输入如下内容：

```python
from machine import Pin
import time

pin = Pin(2, Pin.OUT)
    
while True:
    pin.on()
    time.sleep(1)
    pin.off()
    time.sleep(1) 
```

如下图：

![led闪烁](https://imgs.boringhex.top/blog/202305071005498.png)

保存后，先点击`运行当前脚本`按钮，看一下写的是否有错误，正确的话此时led就会闪烁了。

按一下开发板的复位键，程序发现程序就可以开机运行了。
