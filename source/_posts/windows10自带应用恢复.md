---
title: windows10自带应用恢复
date: 2023-02-23 10:13:57
categories:
tags:
  - windows
  - powershell
---

这两天折腾电脑换系统盘，原来的系统盘是256G的，换了个1T的。

迁移系统用的[傲梅轻松备份](https://www.abackup.com/easy-backup-personal.html)，一切都很顺滑，迁移后系统可以直接正常使用。但是，但是，不知道昨天哪个步骤搞的，似乎是把之前的系统盘格式化擦除导致的，系统自带的应用没了！之前非常想卸载这些自带应用，但是现在自己莫名其妙的没了，心中暗暗不爽，关键是应用商店和windows terminal也没了，这怎么行？！

以管理员身份运行powershell，使用以下命令修复：

``` powershell
Get-AppxPackage -AllUsers | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"}
```

这个命令会安装全部自带应用，如果只需要安装特定应用，可以将上面的命令分开：

``` powershell
Get-AppxPackage -allusers | Select Name, PackageFullName
```

然后找到想修复的应用，再执行：

``` powershell
# 以"WindowsStore"为例
Get-AppxPackage *WindowsStore* -AllUsers | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"}
```
