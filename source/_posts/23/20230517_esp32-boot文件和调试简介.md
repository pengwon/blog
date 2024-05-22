---
title: esp32-boot文件和调试简介
date: 2023-05-17 12:00:35
categories:
tags:
    - esp32
    - micropython
    - python
---

## 1. 将文件下载到ESP32中

首先，我们要将开发板通过数据线链接到电脑上面，通过配置界面将esp32配置到Thonny中，如下：

![Thonny配置](https://imgs.boringhex.top/blog/202305070952091.png)

<!-- more -->

配置完成后，就可以在Thonny中和esp32进行交互了，

![Thonny连接esp32](https://imgs.boringhex.top/blog/202305070952870.png)

上一章节我们完成了在boot.py文件中写hello打印程序，但是我们不能把复杂的程序都写在一个文件中，现在我们演示将多个py文件导入到esp中的方法：

用thonny将文件放到esp32的方法常见的有3种：

第一种：直接在esp32中新建文件或者文件夹：

![直接在esp32中新建文件](https://imgs.boringhex.top/blog/202305070953079.png)

第二种：在ide上新建文件，保存的时候选择到esp32

![保存时选择esp32](https://imgs.boringhex.top/blog/202305070953902.png)

第三种：将已经写好的文件下载到esp32

因为Thonny没有智能提示功能，因此有些人喜欢先用别的ide写，然后下载到ESP32中，下面就介绍这种方法：

首先将电脑的目录选择要下载的文件或文件夹目录，然后右击，先择`上传到`,文件就上传到esp32中了，如下：

![通过Thonny上传](https://imgs.boringhex.top/blog/202305070953762.png)

同样，删除esp32的文件，或者将esp32文件回传到pc中，方法如下：

![通过Thonny删除](https://imgs.boringhex.top/blog/202305070954762.png)

## 2. 运行和boot.py文件

上面讲完了下载，下面就讲调试。

我们都知道，python文件是可以单独运行的，同理，我们也可以单独运行任何一个py文件，如下：

![点击运行](https://imgs.boringhex.top/blog/202305070955370.png)

上面的文件运行，都是通过点击运行按钮来运行的，但是当我们将程序烧录到esp32后重启，程序将自己运行，那从哪个文件里面开始运行呢，python没有像c语言那样，提供给用户一个main函数，而是要由用户指定运行开始文件，因此Micropython就指定默认从boot.py文件启动，这个就是boot.py文件的作用。

记住当esp32上电启动后，程序从boot.py文件启动，因此需要在boot.py写开始程序。

## 3. 调试

Thonny的调式运行，只支持本地的python环境，但是对于本地开发其实有更好的IDE，如VSCode或者Pycharm。

Micropython在esp32上面，调试只能通过print打印来实现调试，这个很坑，但是没有办法。

因此Micropython在esp32上跑局限性很大，而且速度也很慢，不太适合写复杂程序。但是优点也很突出，那就是开发速度真的很快。
