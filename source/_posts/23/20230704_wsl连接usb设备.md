---
title: wsl连接usb设备
date: 2023-07-04 10:08:13
categories:
tags:
    - esp32
    - stm32
    - 嵌入式
    - 工具
---

前两天调试在Windows上的docker容器中连接usb设备，发现在Windows上可以找到设备，但是在容器中找不到设备，怀疑是系统usb设备资源没有挂载到容器中。经搜索，确定wsl并不直接支持USB设备的连接，这就意味着我们必须寻找一个解决方案，而usbipd-win就是提供这个解决方案的工具。它支持在Windows计算机上设置USB/IP项目，以支持一些常见的开发场景，比如刷写Arduino或访问智能卡读取器等。

## [usbipd-win](https://github.com/dorssel/usbipd-win)

> Windows software for sharing locally connected USB devices to other machines, including Hyper-V guests and WSL 2.

在工作和开发中，我们经常需要将USB设备连接到不同的操作系统中，包括常用的Linux发行版。近期，Microsoft的一项开源项目——usbipd-win，打开了一条通向将USB设备连接到WSL 2 Linux发行版的大门。

<!-- more -->

### 安装

此软件需要Microsoft Windows 8.1 x64 / Microsoft Windows Server 2012或更高版本; 它不依赖于任何其他软件。

在连接 USB 设备的 Windows 计算机上运行[最新版本的安装程序（.msi）](https://github.com/dorssel/usbipd-win/releases/latest)。

或者，使用 Windows 程序包管理器：

```powershell
winget install usbipd
```

这将安装：

- 名为（显示名称：USBIP 设备主机）的服务。可以使用 Windows 中的“服务”应用检查此服务`usbipd`的状态。
- 命令行工具 。此工具的位置将添加到环境变量中。
- 调用防火墙规则，以允许所有本地子网连接到服务。可以修改此防火墙规则以微调访问控制。如果使用的是第三方防火墙，则可能需要将其重新配置为允许 TCP 端口 3240 上的传入连接。

然后，在wsl linux 或 linux容器中安装客户端软件和usb硬件标识符数据库，以Ubuntu为例：

```bash
sudo apt install linux-tools-generic hwdata
sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/*-generic/usbip 20
```

注意不同的分发版可能需要[不同的usbip客户端包](https://github.com/dorssel/usbipd-win/wiki/WSL-support#usbip-client-tools)。

此时，一个服务正在Windows上运行以共享usb设备，并且在wsl中安装了必要的工具来连接到共享设备。

### 使用

1. 以管理员身份打开powershell，运行以下命令列出所有连接到Windows的usb设备：

   ```powershell
   ❯ usbipd wsl list
   BUSID  VID:PID    DEVICE                                                        STATE
   2-2    1a2c:0e24  USB 输入设备                                                  Not attached
   2-3    046d:c52b  Logitech USB Input Device, USB 输入设备                       Not attached
   2-5    0bda:5678  Integrated Webcam                                             Not attached
   2-11   187c:0550  USB 输入设备                                                  Not attached
   2-14   0000:0002  未知 USB 设备(设备描述符请求失败)                             Not attached
   5-1    0483:5740  USB 串行设备 (COM5)
   ```

2. 找到要挂载到wsl或容器中的usb设备总线id，然后运行以下命令：

   ```powershell
   # 这里以挂载 5-1 这个usb转串口设备为例，挂载到默认wsl分发版（这里是Ubuntu）中
   ❯ usbipd wsl attach --busid 5-1
   usbipd: info: Using default WSL distribution 'Ubuntu'; specify the '--distribution' option to select a different one.
   ```

   有类似上面的输出即是挂载成功了。需要注意在挂载前要先打开一个wsl终端，这样才能激活linux实例，否则会报：

   ```powershell
   usbipd: error: The selected WSL distribution is not running; keep a command prompt to the distribution open to leave it running.
   ```

3. 在wsl终端中运行以下命令列出连接的usb设备：

   ```bash
   $ lsusb
   Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
   Bus 001 Device 002: ID 0483:5740 STMicroelectronics Virtual COM Port
   Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
   ```

   `0483:5740`这个虚拟串口设备即是通过usbipd挂载进来的。

4. 在wsl中完成usb设备使用后，可以直接拔出usb设备物理断开，也可以在powershell中运行以下命令卸载usb设备：

   ```powershell
   usbipd wsl detach --busid <busid>
   ```

### 卸载软件包

可以通过控制面板或系统设置来卸载`usbipd`软件包，也可以通过`winget`包管理工具：

```powershell
winget uninstall usbipd
```

进一步了解这个软件包的工作原理，可以参阅 [Windows命令行博客](https://devblogs.microsoft.com/commandline/connecting-usb-devices-to-wsl/#how-it-works)。
