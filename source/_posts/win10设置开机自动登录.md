---
title: win10设置开机自动登录
date: 2023-05-05 17:28:43
categories:
tags:
  - windows
  - powershell
---

今天用Windows任务计划程序部署一个python服务时，总是报网络错误，而登录后再启动就不会出这个问题，所以就想设置开机自动登录。

## 设置方法

1. <kbd>Win</kbd> + <kbd>R</kbd>，然后在弹出的输入框中输入`netplwiz`，回车运行：
   
  ![运行`netplwiz`](https://imgs.boringhex.top/blog/20230428171851.png)
  
<!-- more -->

2. 在弹出的窗口中选择要自动登录的用户，然后取消勾选“要使用本计算机，用户必须输入用户名和密码”：

  ![设置自动登录](https://imgs.boringhex.top/blog/20230428172135.png)

3. 在弹出的窗口中输入上一步对应的用户名和密码，并确定：

  ![输入用户名和密码](https://imgs.boringhex.top/blog/20230428172343.png)

这样设置后开机就自动登录了。

> 新版系统默认取消了上述方法第2步中的选项，需要先用下面的方法激活这个选项。

## 激活自动登录选项

方法是修改注册表：

1. <kbd>Win</kbd> + <kbd>R</kbd>，然后在弹出的输入框中输入`regedit`，回车运行，打开注册表。

2. 依次展开 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsNT\CurrentVersion\PasswordLess\Device`：

  ![注册表](https://imgs.boringhex.top/blog/20230428174350.png)

3. 将`DevicePasswordLessBuildVersion`的值改为0，并点击“确定”：

  ![修改`DevicePasswordLessBuildVersion`](https://imgs.boringhex.top/blog/20230428174547.png)

  此时自动登录选项就出来了，再按照前面介绍的方法选择用户取消勾选即可：

  ![取消勾选](https://imgs.boringhex.top/blog/20230428174956.png)
