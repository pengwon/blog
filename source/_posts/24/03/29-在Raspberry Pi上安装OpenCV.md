---
title: 在Raspberry Pi上安装OpenCV
date: 2024-03-29 11:24:53
categories:
tags:
    - diy
    - 算法
---


这是一个快速指南，展示了如何在 Raspberry Pi 板上安装 OpenCV（开放计算机视觉库）软件。我们在 Raspberry Pi OS 64 位上测试了此安装。OpenCV 是一个用于计算机视觉和机器学习的开源库，带有用于图像处理、视频分析和机器学习应用程序的工具和算法。

![树莓派安装OpenCV](https://imgs.boringhex.top/blog/20240329100501.png)

在 Raspberry Pi 上安装 OpenCV 的方法有很多种，您可以在虚拟环境中使用 pip 安装，使用 apt install 安装或从源代码编译软件。在本指南中，我们将展示如何使用`pip`在虚拟环境中安装OpenCV：

## 先决条件

在继续之前：

- 你需要一个 Raspberry Pi 板。
- 您应该有一个运行 Raspberry Pi OS（32 位或 64 位）的 Raspberry Pi。
- 您应该能够与 Raspberry Pi 建立 SSH 连接。

## 在 Raspberry Pi 上使用 pip 在虚拟环境中安装 OpenCV

建立SSH连接后，更新和升级软件包：

```bash
sudo apt update && sudo apt upgrade -y
```

<!-- more -->

运行下一个命令，该命令应在终端中打印 Python 版本 3.X：

```bash
python --version
```

安装 pip3 和 Python 3 虚拟环境：

```bash
sudo apt install -y python3-pip python3-virtualenv
```

### 创建虚拟环境

我们将在虚拟环境中安装 OpenCV 库。创建一个虚拟环境会将我们正在使用的 Python 库（在本例中为 OpenCV 库）与系统的其余部分隔离开来。

我们将在桌面上的目录上创建虚拟环境。在“终端”窗口中输入以下命令以移动到桌面：

```bash
cd ~/Desktop
```

为您的项目创建一个文件夹。我们将在此处创建虚拟环境并安装库。我们将创建一个名为 projects 的文件夹。

```bash
mkdir projects
```

移动到新创建的文件夹：

```bash
cd ~/Desktop/projects
```

为此目录创建一个名为myenv的.这必须是我们将安装 OpenCV 库的同一目录。取代myenv的替换为虚拟环境的所需名称。

```bash
python3 -m venv projectsenv
```

然后，您可以运行以下命令来检查虚拟环境是否存在。

```bash
ls -l
```

![](https://imgs.boringhex.top/blog/20240329101400.png)

激活虚拟环境：

```bash
source projectsenv/bin/activate
```

您的提示应更改，以指示您现在处于虚拟环境中。

![](https://imgs.boringhex.top/blog/20240329101439.png)

### 安装 OpenCV 库

现在我们处于虚拟环境中，我们可以安装 OpenCV 库。运行以下命令：

```bash
pip3 install opencv-contrib-python
```

几秒钟后，将安装该库（忽略有关已弃用软件包的任何黄色警告）。

![](https://imgs.boringhex.top/blog/20240329101538.png)

您已经准备好开始编写 Python 代码了。

### 测试 OpenCV pip 安装

现在让我们确保 OpenCV 已成功安装在您的虚拟环境中。在终端窗口中，启动 Python：

```bash
python
```

然后，在 Python 中导入 OpenCV 库。

```bash
import cv2
```

最后，键入下一个命令以检查您安装的 OpenCV 版本。

```bash
cv2.__version__
```

如果一切都已正确完成，它应该返回您的 OpenCV 版本，如下图所示。

![](https://imgs.boringhex.top/blog/20240329101710.png)

您可以通过键入以下命令退出 Python 提示符：

```bash
exit()
```