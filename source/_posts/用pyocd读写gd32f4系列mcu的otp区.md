---
title: 用pyocd读写gd32f4系列mcu的otp区
date: 2023-04-13 12:20:28
categories:
tags:
  - ci/cd
  - python
  - 嵌入式
---

如[前一篇文章](https://blog.boringhex.top/posts/e881fb64e09b/)所述，pyocd是一个调试、编程cortex-m单片机的简单、强大的工具，本文就结合实例讲解pyocd的一些用法。

使用j-link、dap-link等工具在keil或其它ide中调试单片机程序的场景比较常见，而使用这些工具对单片机片内和片外flash存储区进行编程在生产环节也是非常重要的步骤，本文就以gd32f4系列单片机otp区的烧写为例，讲解pyocd的用法。

<!-- more -->

## 准备工作

pyocd并没有内置对gd32f4系列单片机的支持，需要先安装包：

``` powershell
# 以gd32f425为例进行查找
❯ pyocd pack find gd32f425
  Part         Vendor       Pack                      Version   Installed  
---------------------------------------------------------------------------
  GD32F425RE   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True
  GD32F425RG   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True
  GD32F425RK   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True
  GD32F425VG   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True
  GD32F425VK   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True
  GD32F425ZG   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True
  GD32F425ZK   GigaDevice   GigaDevice.GD32F4xx_DFP   3.0.3     True

# 我已经安装了，所以上面输出的"Installed"为"True"
# 没有安装的话需要进行安装
# 安装后再执行上面的命令应该输出相似的结果
❯ pyocd pack install GigaDevice.GD32F4xx_DFP
```

这样就可以用pyocd连接gd32f4系列的mcu了。

使用 j-link 或 dap-link 等 pyocd 支持的[调试器](https://pyocd.io/docs/debug_probes.html)连接mcu，还是以gd32f425为例：

``` powershell
# 列出当前连接的调试器
❯ pyocd list
  #   Probe/Board            Unique ID      Target  
----------------------------------------------------
  0   wch.cn WCH CMSIS-DAP   0001A0000001   n/a     

# 以命令模式连接目标器件
❯ pyocd cmd -t gd32f425rg
0002472 W Invalid coresight component, cidr=0x0 [rom_table]
Connected to GD32F425RG [Running]: 0001A0000001

# 查看存储区map
pyocd> show map
 Region         Type   Start       End         Size        Access  Sector      Page       
 IROM1_0x4000   Flash  0x08000000  0x0800ffff  0x00010000  rx      0x00004000  0x00000400
 IROM1_0x10000  Flash  0x08010000  0x0801ffff  0x00010000  rx      0x00010000  0x00000400
 IROM1_0x20000  Flash  0x08020000  0x080fffff  0x000e0000  rx      0x00020000  0x00000400
 IRAM2          Ram    0x10000000  0x1000ffff  0x00010000  rwx     -           -
 IRAM1          Ram    0x20000000  0x2002ffff  0x00030000  rwx     -           -
```

这样就已经可以对gd32f425的片内存储区进行编程了。但是如果需要对otp区编程，除了单片机的DFP包以外，还需要用户脚本和otp区编程算法文件，可以[点击这里](https://download.csdn.net/download/Fei_Yang_YF/87685260)下载。

## OTP编程

将下载的压缩包解压至工作目录：

``` powershell
# cd进入压缩包解压的工作目录
❯ ls

    Directory: C:\Users\flashalgo\gd32f4xx_otp

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
la---           2022/9/15    18:40          13772 GD32F4xx_OTP.FLM
la---           2022/9/15    18:28            265 pyocd_user.py


# 再次以命令模式连接gd32f425目标板
❯ pyocd cmd -t gd32f425rg
0002408 W Invalid coresight component, cidr=0x0 [rom_table]
Connected to GD32F425RG [Running]: 0001A0000001

# 再次查看存储区map
pyocd> show map
 Region         Type   Start       End         Size        Access  Sector      Page
 IROM1_0x4000   Flash  0x08000000  0x0800ffff  0x00010000  rx      0x00004000  0x00000400
 IROM1_0x10000  Flash  0x08010000  0x0801ffff  0x00010000  rx      0x00010000  0x00000400
 IROM1_0x20000  Flash  0x08020000  0x080fffff  0x000e0000  rx      0x00020000  0x00000400
 IRAM2          Ram    0x10000000  0x1000ffff  0x00010000  rwx     -           -
 otp            Flash  0x1fff7800  0x1fff79ff  0x00000200  rx      0x00000200  0x00000200
 IRAM1          Ram    0x20000000  0x2002ffff  0x00030000  rwx     -           -
```

可以看到otp区已经加进来了，这样就可以对otp区进行编程了。

我们先来读一下：

``` powershell
# 依然在命令连接模式
pyocd> rb 0x1fff7800
1fff7800:  fe                                                 |.|

pyocd> read16 0x1fff7800
1fff7800:  fffe                                       |..|

pyocd> read32 0x1fff7800
1fff7800:  fffffffe                               |....|
```

正常情况如果之前没对otp区进行过编程的话，读到的应该是全"1"，即otp区所有字节都是`0xff`，我这个板子因为之前编程过，首字节最低位被写入"0"了。

接下来我们把第一个字节的第2位写入"0"：

``` powershell
# 写入'1111 1101'，即'0xfd'
pyocd> wb 0x1fff7800 0xfd

# 再次读出
pyocd> rb 0x1fff7800
1fff7800:  fc                                                 |.|
```

我们发现读出的是`0xfc`，并不是写入的`0xfd`，这是因为otp区只能被编程1次，每一位只能从"1"写成"0"，不能从"0"再写回"1"。

至此，我们已经对gd32f4系列单片机的otp区进行编程了。需要注意的是，从兆易官方手册上，gd32f403xx系列是没有otp的，其它目前已发布的gd32f4xx系列mcu都是1个512B的OTP区，所以这个算法文件可以支持gd32f4xx全系列。
