---
title: powershell模块安装问题
date: 2023-02-09 18:08:02
categories:
tags:
  - powershell
---

今天在安装powershell模块时遇到一个问题：

```powershell
❯ Install-Module -Name PowerShellAI
Install-Package: Unable to find module providers (PowerShellGet).
```

<!-- more -->

提示找不到模块提供者，这个问题出在`PSModulePath`环境变量上，可以先看下这个环境变量：

```powershell
❯ $env:PSModulePath
C:\Users\pengwon\Documents\PowerShell\Modules;C:\Program Files\PowerShell\Modules;C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules;
```

因为`Install-Module`命令需要`PowerShellGet`模块，而通过全局搜索"PowerShellGet"，发现包含“PowerShellGet.psd1”文件的路径并没有在上面的环境变量中。 

![](https://imgs.boringhex.top/blog/20230209180543.png)

解决就比较简单了，把“C:\Program Files\PowerShell\7\Modules“加到`PSModulePath`环境变量中就可以了：

```powershell
$env:PSModulePath += ";C:\Program Files\PowerShell\7\Modules"
```

这个是临时添加，仅对当前会话有效。如果需要永久添加，可以从控制面板中编辑环境变量。
