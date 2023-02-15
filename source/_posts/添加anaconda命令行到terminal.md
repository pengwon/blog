---
title: 添加anaconda命令行到terminal
date: 2023-02-15 10:43:46
categories:
tags:
  - powershell
  - 终端/terminal
  - 命令行/cli/cmd
  - 控制台/console
  - windows
---

在Windows平台上创建python开发环境，通常会选择anaconda，用起来十分方便。

安装anaconda后，会自动在"开始"菜单创建2个命令行快捷方式：

![](https://imgs.boringhex.top/blog/20230215105832.png)

上面的是powershell，下面的是cmd。

<!-- more -->

点击打开，powershell是这样的：

![](https://imgs.boringhex.top/blog/20230215110617.png)

cmd是这样的：

![](https://imgs.boringhex.top/blog/20230215110738.png)

能用，但不太现代，是Windows命令行终端饱受诟病的样子。

[Windows Terminal](https://learn.microsoft.com/zh-cn/windows/terminal/)发布后，算是挽回了一些面子，好看，也好用，现在已经是win11的默认终端程序了。

## 添加conda PowerShell到terminal

将以下内容复制粘贴到terminal的配置文件`settings.json`中`profiles`下：

``` json
{
  "commandline": "pwsh.exe -ExecutionPolicy ByPass -NoExit -Command \"& '%USERPROFILE%\\anaconda3\\shell\\condabin\\conda-hook.ps1' ; conda activate '%USERPROFILE%\\anaconda3' \"",
  "icon": "%USERPROFILE%\\anaconda3\\Menu\\anaconda-navigator.ico",
  "name": "Anaconda PowerShell",
  "startingDirectory": "%USERPROFILE%"
}
```

实际效果：

![](https://imgs.boringhex.top/blog/20230215141313.png)

## 添加conda命令提示符到terminal

将以下内容复制粘贴到terminal的配置文件`settings.json`中`profiles`下：

``` json
{
  "commandline": "cmd.exe /k \"%USERPROFILE%\\anaconda3\\Scripts\\activate.bat %USERPROFILE%\\anaconda3\"",
  "icon": "%USERPROFILE%\\anaconda3\\Menu\\Iconleak-Atrous-Console.ico",
  "name": "Anaconda Prompt",
  "startingDirectory": "%USERPROFILE%"
}
```

实际效果：

![](https://imgs.boringhex.top/blog/20230215141436.png)

有几点需要注意的地方：

1. 如果没有安装powershell7等新版powershell，需要将上面`pwsh.exe`替换为`powershell.exe`等旧版程序名。
2. 在`settings.json`配置文件中添加新的`profiles`时，不要忘记逗号`,`间隔符。
3. 示例中的路径是anaconda安装默认路径，如果安装在别的路径下了，需要修改为实际路径。
4. 启动时默认为`base`环境，如需切换可以用`conda activate`命令，或修改配置文件为自己常用的环境。
