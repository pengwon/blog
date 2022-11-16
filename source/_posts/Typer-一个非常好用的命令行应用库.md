---
title: 'Typer: 一个非常好用的命令行应用库'
tags:
  - 命令行/CLI/cmd
  - 控制台/Console
  - 终端/Terminal
  - python
  - powershell
date: 2022-11-16 16:13:54
categories:
---

> [Typer](https://github.com/tiangolo/typer), build great CLIs. Easy to code. Based on Python type hints.

今天偶然看到GitHub上一个python开源项目——[Typer](https://github.com/tiangolo/typer)，可以帮助简单快速地构建命令行应用。正如介绍所说，“users will love using and developers will love creating”。

> The key features are:
> 
> - **Intuitive to write**: Great editor support. Completion everywhere. Less time debugging. Designed to be easy to use and learn. Less time reading docs.
> 
> - **Easy to use**: It's easy to use for the final users. Automatic help, and automatic completion for all shells.
> 
> - **Short**: Minimize code duplication. Multiple features from each parameter declaration. Fewer bugs.
> 
> - **Start simple**: The simplest example adds only 2 lines of code to your app: 1 import, 1 function call.
> 
> - **Grow large**: Grow in complexity as much as you want, create arbitrarily complex trees of commands and groups of subcommands, with options and arguments.

<!-- more -->

Typer算是出身名门，跟很火的快速web框架[FastAPI](https://fastapi.tiangolo.com/)同一开发团队，是FastAPI的小兄弟，旨在成为CLI中的FastAPI。

## 安装

Typer需要Python 3.6+。

安装过程很丝滑：

``` powershell
❯ pip install "typer[all]"
---> 100%
Successfully installed typer
```

建议大家给pip更换清华源，快的不是一星半点。

- 临时

``` powershell
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

- 全局

``` powershell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

- 配置多个镜像源

``` powershell
pip config set global.extra-index-url "<url1> <url2>..."
```

注意源地址间有空格，可用`pypi`镜像源列表：https://mirrors.cernet.edu.cn/list/pypi
