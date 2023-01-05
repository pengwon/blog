---
title: Scoop-强大的Windows命令行包管理工具
date: 2023-01-04 14:48:24
categories:
tags:
  - Windows
  - powershell
  - 包管理
---

对于开发者来说，包管理工具是绕不开的话题，Ubuntu广受喜爱的原因之一也包含其方便的`apt`包管理工具，MacOS上有`homebrew`供开发者使用，这些包管理工具让开发环境的搭建非常顺滑。这里的包，是指广义的软件包，不仅包含常见的图形用户界面（GUI）软件，也包含命令行界面（CLI）软件和工具。

而开发者苦Windows命令行甚至powershell久矣，在早前的版本上，真的没有特别好用的工具。如今，终于也有了历经时间和用户考验的包管理器：Chocolatey，Scoop和Winget。Winget时间不久，由微软官方社区维护，但目前人气不如前两者。我只简单使用过chocolatey，没太多发言权，但scoop真的很好很强大，并且现在scoop的社区非常活跃，参与者众多，维护更新很快。我在GitHub上的第一次PR就提给了scoop。

<!-- more -->

## [Scoop](https://scoop.sh/)

scoop的官网上这样介绍自己：

> A command-line installer for Windows

### Quickstart

scoop要求powershell 5.1+，所以如果没有安装powershell的话，还需要先[安装powershell](https://learn.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.2)。这里要注意powershell的版本区别，强烈建议安装powershell 7，最好用lts版本。此外，强烈安利[Windows Terminal](https://learn.microsoft.com/zh-cn/windows/terminal/install)，它已经是Windows 11默认终端程序，绝对值得拥有。

准备好后，打开powershell终端，运行：

``` powershell
> Set-ExecutionPolicy RemoteSigned -Scope CurrentUser # Optional: Needed to run a remote script the first time
> irm get.scoop.sh | iex
```

scoop默认安装到用户目录，这样可以避免权限问题。如果需要配置高级安装选项，请参考这里的[安装方法](https://github.com/ScoopInstaller/Install#readme)。

### What does Scoop do

那么scoop可以做些什么呢？scoop从命令行安装你熟悉和喜爱的程序，非常丝滑，它的特点包括：

- 消除权限弹窗，安装过程超丝滑
- 隐藏GUI安装向导，一步到位
- 防止安装大量程序的PATH环境变量污染
- 避免安装和卸载程序的意外副作用
- 自动查找并安装依赖项
- 自动执行额外设置步骤以获得可用程序

在之前的[Hexo安装和配置](https://blog.boringhex.top/posts/d30a586cac66/)中就曾讲过，从nodejs官网安装node.js各种坑，最后scoop一条命令搞定，那感觉真是太爽了。

``` powershell
PS C:\> scoop install nodejs                                                    
Installing 'nodejs' (18.4.0) [64bit]                                            
node-v18.4.0-win-x64.7z (17.3 MB) [===================================] 100%    
Checking hash of node-v18.4.0-win-x64.7z ... ok.                                
Extracting node-v18.4.0-win-x64.7z ... done.                                    
Linking ~\scoop\apps\nodejs\current => ~\scoop\apps\nodejs\18.4.0               
Persisting bin                                                                  
Persisting cache                                                                
Running post_install script...                                                  
'nodejs' (18.4.0) was installed successfully!
```

安装vs code：

``` powershell
PS C:\> scoop bucket add extras                                                 
Checking repo... OK                                                             
The extras bucket was added successfully.                                       
PS C:\> scoop install vscode                                                    
Installing 'vscode' (1.68.1) [64bit]                                            
dl.7z (108.1 MB) [====================================================] 100%    
Checking hash of dl.7z ... ok.                                                  
Extracting dl.7z ... done.                                                      
Linking ~\scoop\apps\vscode\current => ~\scoop\apps\vscode\1.68.1               
Creating shortcut for Visual Studio Code (code.exe)                             
Persisting data                                                                 
Running post_install script...                                                  
'vscode' (1.68.1) was installed successfully!
```

安装nerd-fonts字体：

``` powershell
PS C:\> scoop bucket add nerd-fonts                                             
Checking repo... OK                                                             
The nerd-fonts bucket was added successfully.                                   
PS C:\> scoop install Cascadia-Code                                             
Installing 'Cascadia-Code' (2111.01) [64bit]                                    
CascadiaCode-2111.01.zip (23.7 MB) [===================================] 100%   
Checking hash of CascadiaCode-2111.01.zip ... ok.                               
Extracting CascadiaCode-2111.01.zip ... done.                                   
Running installer script...                                                     
Linking ~\scoop\apps\Cascadia-Code\current => ~\scoop\apps\Cascadia-Code\2111.01
'Cascadia-Code' (2111.01) was installed successfully!
```

强烈建议安装这个字体，并在写代码或终端和编辑器中使用，看着真舒服。

Scoop 以可移植的方式下载和管理软件包，将它们整齐地隔离在用户目录scoop文件夹中。它不会在目录外安装文件，但允许将 Scoop 安装放置在像`~\scoop`这样的你喜欢的任何位置。

```powershell
> dir ~\scoop

    Directory: C:\Users\User\scoop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---          02-04-2022    16:06                apps
d----          30-03-2022    13:22                buckets
d----          02-04-2022    16:06                cache
da---          30-03-2022    21:32                persist
da---          02-04-2022    16:06                shims
d----          20-02-2022    01:22                workspace
```

对于终端应用程序，Scoop在文件夹中创建`shims`，这是一种命令行快捷方式，可在PATH中访问。对于图形应用程序，Scoop在专用的“开始”菜单文件夹中创建程序快捷方式，称为“Scoop应用程序”。这样，软件包始终被完全卸载，您可以确定 PATH 和“开始”菜单中当前有哪些工具。`shims`在`~\scoop\shims`目录中。

``` powershell
> scoop search python
Results from local buckets...

Name      Version  Source Binaries
----      -------  ------ --------
python    3.10.5   main
winpython 3.10.4.0 main

> scoop install python
...
Creating shim for 'python.exe'.
'python' (3.10.5) was installed successfully!

> python -c "print('Hello from Python installed by Scoop!')"
Hello from Python installed by Scoop!
```

### Discovering Packages

scoop包作为git仓库的一部分存在，被称为`buckets`。内置的[包搜索](https://scoop.sh/#/apps)命令可以用来搜索GitHub上的全部程序，GitHub上的所有`buckets`列表见[这里](https://scoop.sh/#/buckets)。

``` powershell
> scoop search mongo
Results from local buckets...

Name                   Version Source Binaries
----                   ------- ------ --------
mongodb-compass        1.32.2  extras
mongosh                1.5.0   extras
mongodb-database-tools 100.5.3 main
mongodb                5.3.2   main

> scoop search citra
Results from other known buckets...
(add them using 'scoop bucket add <name>')

Name         Source
----         ------
citra-canary games
citra        games
```

### Creating Packages

我们还可以用scoop来创建和发布自己的程序，这样就可以非常方便的在团队内部创建和分享小工具。

``` powershell
> scoop create https://example.com/foobar/1.2.3/foobar-package.zip
1) foobar
2) 1.2.3
3) foobar-package.zip
App name: 1
1) foobar
2) 1.2.3
3) foobar-package.zip
Version: 2
Created 'C:\Users\User\Desktop\foobar.json'.
```

scoop中的程序用一个简单的json文件来维护，里边还可以嵌入powershell语句。

``` powershell
> scoop cat gifski
{
    "version": "1.6.4",
    "description": "GIF encoder based on libimagequant (pngquant).",
    "homepage": "https://gif.ski",
    "license": "AGPL-3.0-or-later",
    "url": "https://gif.ski/gifski-1.6.4.zip",
    "hash": "dc97c92c9685742c4cf3de59ae12bcfcfa6ee08d97dfea26ea88728a388440cb",
    "pre_install": "if (!(Test-Path '$dir\\config')) { New-Item '$dir\\config' }",
    "bin": "gifski.exe",
    "checkver": "For Windows.*?gifski-([\\d.]+)\\.zip",
    "autoupdate": {
        "url": "https://gif.ski/gifski-$version.zip"
    }
}
```

scoop的工作流是非常非常依赖GitHub吧，国内访问偶尔抽风，如果大家在`irm get.scoop.sh | iex`这一步安装出问题，可以换成下面的命令试一下：

``` powershell
irm https://gitee.com/happy-peter/InstallScoop/raw/master/install.ps1 | iex
```
