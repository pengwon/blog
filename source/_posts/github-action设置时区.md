---
title: github action设置时区
date: 2023-02-02 12:10:25
categories:
tags:
  - github
  - ci/cd
  - hexo
---

hexo通过插件支持很多种部署方式，比如GitHub Page、腾讯云COS、阿里云OSS等等，我最后选择通过GitHub Actions实现自动部署。

但是在编写[STM32 裸机编程指南系列](https://blog.boringhex.top/posts/2abdb43ecdc4/)时发现一个问题，GitHub Actions产生永久链接跟本地的不一致，这给文章间的引用造成了很大麻烦。

因为我配置hexo的永久链接为：`permalink: posts/:hash/`，而hexo在计算hash时是通过文件名和时间，所以应该是GitHub Actions系统时间与本地不同导致的。

在 GitHub Actions 中，可以通过设置环境变量来指定时区。

<!-- more -->

下面是一个示例，将时区设置为 Asia/Shanghai：

``` yaml
name: Specifying Timezone

on: [push]

env:
  TZ: Asia/Shanghai

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Show the timezone
      run: |
        echo "The timezone is $TZ"
        date
```

在这个例子中，我们通过设置环境变量`TZ`来指定时区，并在后面的步骤中使用`date`命令显示当前的时间和时区。

请注意，如果要使用的时区不在标准的时区数据库中，你可能需要添加对应的时区数据库文件。

下面是一个示例，添加了自定义时区数据库文件 Asia/Shanghai：

``` yaml
name: Adding Timezone Database

on: [push]

env:
  TZ: Asia/Shanghai

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Check the current timezone database
      run: |
        ls /usr/share/zoneinfo
        echo "---"
        date
        echo "---"

    - name: Add the custom timezone database file
      run: |
        sudo mkdir -p /usr/share/zoneinfo/Asia
        sudo cp /tmp/Asia/Shanghai /usr/share/zoneinfo/Asia/
        sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

    - name: Show the timezone
      run: |
        echo "The timezone is $TZ"
        date
```

在这个例子中，我们首先使用`ls /usr/share/zoneinfo`命令检查当前的时区数据库，然后使用`sudo mkdir、sudo cp 和 sudo ln -sf`命令来添加自定义的时区数据库文件。最后，我们使用`date`命令显示当前的时间和时区。

请注意，该示例假设自定义的时区数据库文件位于`/tmp/Asia/Shanghai`路径。请根据实际情况调整该路径。
