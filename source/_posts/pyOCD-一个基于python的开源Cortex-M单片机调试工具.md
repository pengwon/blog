---
title: pyOCD-一个基于python的开源Cortex-M单片机调试工具
date: 2023-04-12 19:09:11
categories:
tags:
  - ci/cd
  - python
  - 嵌入式
---

[pyOCD](https://pyocd.io/) 是一个基于 Python 的工具和 API，用于调试、编程和探索 Arm Cortex 微控制器。它支持通过命令行或 IDE（如 VSCode Cortex-Debug 插件和 Eclipse Embedded CDT）使用 gdb 进行调试，支持将固件和数据镜像编程到内部或外部闪存中，也可以将镜像加载到 RAM 中。它还提供了一个简单的 API，非常适合用于 CI、定制测试和调试工具、安全研究等。此外，pyOCD 还支持 Semihosting 和 SWV，具有完整的半主机实现，包括控制台和文件 I/O。它内置了对超过 70 种流行 MCU 的支持，并且通过使用 CMSIS 设备族包，几乎市面上所有的 Cortex-M 设备都能得到支持。

<!-- more -->

## [pyOCD](https://pyocd.io/)

### 特性

- gdbserver
  - 支持在命令行或IDE中使用`gdb`进行调试，可以在VSCode [Cortex-Debug](https://marketplace.visualstudio.com/items?itemName=marus25.cortex-debug) 插件和[Eclipse Embedded CDT](https://projects.eclipse.org/projects/iot.embed-cdt)中使用。
- flash programming
  - 将固件和数据映像烧写到内部或外部flash，也可以载入到ram中。
- python API
  - 通过简单的 API 完全控制目标设备，高度 适用于 CI、定制测试和调试工具、安全研究等。
- semihosting and SWV
  - 具有控制台和文件 I/O 的完整半托管实现。 SWV 基本 printf 输出， 通过TCP/IP流式传输原始SWO数据，在Python中构建SWO事件处理数据流图。
- CMSIS-Packs
  - 访问 [CMSIS 设备系列包](https://www.keil.com/dd2/pack/)支持的全套 Arm Cortex-M 设备。包装罐 由 pyOCD 管理并按部件号安装，或自行下载包。
- built-in devices
  - 内置了对 70 多种常见 Arm Cortex-M 设备的支持。

### 命令行工具

`pyocd`命令行工具通过以下子命令提供了对目标设备的完全控制：

- `gdbserver`: GDB remote server allows you to debug using gdb via either the console or
    [several GUI debugger options](https://pyocd.io/docs/gdb_setup).
- `load`: Program files of various formats into flash or RAM.
- `erase`: Erase part or all of an MCU's flash memory.
- `pack`: Manage [CMSIS Device Family Packs](https://open-cmsis-pack.github.io/Open-CMSIS-Pack-Spec/main/html/index.html)
    that provide additional target device support.
- `commander`: Interactive REPL control and inspection of the MCU.
- `server`: Share a debug probe with a TCP/IP server.
- `reset`: Hardware or software reset of a device.
- `rtt`: Stream Segger RTT IO with _any_ debug probe.
- `list`: Show connected devices.

### API

python API提供了以下特性：

-  halt, step, resume control
-  read/write memory
-  read/write core registers
-  set/remove hardware and software breakpoints
-  set/remove watchpoints
-  write to flash memory
-  load binary, hex, or ELF files into flash
-  reset control
-  access CoreSight DP and APs
-  SWO and SWV
-  and more!

可以通过[配置文件](https://pyocd.io/docs/configuration)和[用户脚本](https://pyocd.io/docs/user_scripts)来实现更多个性化功能。

### Requirements

- Python 3.6.0 or later.
- macOS, Linux, Windows 7 or newer, or FreeBSD
- A recent version of [libusb](https://libusb.info/). See [libusb installation](#libusb-installation) for details.
- Microcontroller with an Arm Cortex-M CPU
- Supported debug probe
  - [CMSIS-DAP](https://arm-software.github.io/CMSIS_5/DAP/html/index.html) v1 (HID) or v2 (WinUSB), including:
    - Atmel EDBG/nEDBG
    - Atmel-ICE
    - Cypress KitProg3 or MiniProg4
    - [DAPLink](https://github.com/ARMmbed/DAPLink) based debug probe, either on-board or standalone
    - Keil ULINKplus
    - NXP LPC-LinkII
    - NXP MCU-Link
  - [PE Micro](https://pemicro.com/) Cyclone and Multilink
  - Raspberry Pi Picoprobe
  - SEGGER J-Link
  - STLinkV2 or STLinkV3, either on-board or the standalone versions

Version [0.29](https://github.com/pyocd/pyOCD/releases/tag/v0.29.0) is the last version to support Python 2.

### 安装

最简单的方式就是通过`pip`安装：

``` powershell
pip install -U pyocd
```

使用python建立工作环境时，始终在虚拟环境中进行操作是个好习惯，强烈建议用`conda`或python自带虚拟环境工具建立工作环境。

**更全面的安装说明请参阅 [documentation](https://pyocd.io/docs/installing).**

为树莓派等非 x-86 平台系统安装请参阅[relevant documentation](https://pyocd.io/docs/installing_on_non_x86)。

注意，在不同平台上安装时可能会遇到权限问题，可以参考以下方法解决：

1. Under Linux, run with `sudo -H` to install pyOCD and dependencies globally. 
2. On macOS, installing with `sudo` should never be required, although sometimes permissions can become modified such that installing without using sudo fails.
3. Specify the `--user` option to install local to your user account.
4. Run the command in a [virtualenv](https://virtualenv.pypa.io/en/latest/) local to a specific project working set.

验证下安装：

``` powershell
❯ pyocd -h
usage: pyocd [-h] [-V] [--help-options]  ...

PyOCD debug tools for Arm Cortex devices

optional arguments:
  -h, --help       show this help message and exit
  -V, --version    show program version number and exit
  --help-options   Display available session options.

subcommands:

    commander (cmd)
                   Interactive command console.
    erase          Erase entire device flash or specified sectors.
    load (flash)   Load one or more images into target device memory.
    gdbserver (gdb)
                   Run the gdb remote server(s).
    json           Output information as JSON.
    list           List information about probes, targets, or boards.
    pack           Manage CMSIS-Packs for target support.
    reset          Reset a target device.
    server         Run debug probe server.
    rtt            SEGGER RTT Viewer.

❯ pyocd -V
0.34.3
```
