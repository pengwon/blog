---
title: windows引导管理
date: 2023-02-20 11:51:45
categories:
tags:
  - windows
  - powershell
---

当电脑上安装多个系统时，在启动引导阶段，会出现启动管理器，Windows上是"Windows启动管理器"，Ubuntu等linux发行版上也有他们的启动管理器。

<!-- more -->

Windows启动管理器界面类似这样：

![](https://imgs.boringhex.top/blog/20230220115747.png)

如果需要配置默认启动项，<kbd> win </kbd> + <kbd> r </kbd>，输入`msconfig`，或者在`开始`菜单中搜"系统配置"：

![](https://imgs.boringhex.top/blog/20230220120416.png)

在"引导"选项卡下可以配置默认启动的系统：

![](https://imgs.boringhex.top/blog/20230220120701.png)

应用 → 确定 → 重启，这时默认启动的系统就是上面选择的了。

这个界面默认超时时间是30秒，如果需要修改，也是在上面这一步进行。

如果不想要其它启动项，也是同样的，在上面一步删除对应启动项就可以。

在控制面板的高级系统设置里也有类似选项，可以进行默认启动项的配置。

![](https://imgs.boringhex.top/blog/20230220143946.png)
