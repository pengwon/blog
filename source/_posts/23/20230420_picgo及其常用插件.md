---
title: picgo及其常用插件
date: 2023-04-20 15:49:08
categories:
tags:
  - hexo
  - 工具
---

# PicGo

PicGo 是一款开源的图片上传、管理工具，提供了丰富的图片上传、管理功能，并且支持多家图床。PicGo 可以用来处理 Markdown、Typora、Word、博客等多种文档类型中的图片上传问题，非常方便实用。

## 功能

- 支持多家图床，包括 GitHub、七牛云、腾讯云 COS、阿里云 OSS、SM.MS、imgur 等。
- 支持图片缩放、压缩、水印、WebP 格式转换等功能。
- 支持拖拽上传、剪切板上传、快捷键上传等多种上传方式。
- 支持对已上传的图片进行管理、查看和删除。

<!-- more -->

## 安装

PicGo 可以在 Windows、Mac OS X 和 Linux 上使用。你可以从 GitHub Release 页面或者 PicGo 官网下载对应平台的安装包，或者使用 npm 安装。

使用 npm 安装：

```
npm install picgo -g
```

## 使用

安装完成后，你可以通过 PicGo 的命令行界面或者图形界面来使用。

### 命令行界面

在命令行界面中，你可以使用 PicGo 提供的命令来上传、管理图片。

```
picgo upload <path|url|base64>
```

### 图形界面

在图形界面中，你可以使用 PicGo 提供的图形界面来上传、管理图片。

## 配置

在使用 PicGo 之前，你需要先进行一些配置。例如，你需要选择一个图床来存储你的图片，并且设置相关的参数。

在 PicGo 中，你可以通过编辑配置文件 `config.json` 来进行配置。默认情况下，配置文件位于 `~/.picgo/config.json`，你可以根据自己的需要进行修改。

## 总结

PicGo 是一款非常实用的图片上传、管理工具，支持多家图床，并且提供了丰富的图片上传、管理功能。如果你经常需要在 Markdown、Typora、Word、博客等文档中插入图片，那么 PicGo 绝对是你不可或缺的工具之一。

# PicGo 常用插件介绍

PicGo 是一款强大的图片上传、管理工具，除了本身的功能外，还有很多插件可以扩展其功能。下面介绍一些常用的插件。

## 压缩插件

- [picgo-plugin-compress](https://github.com/Spades-S/picgo-plugin-compress): 压缩上传的图片。

## 缩放插件

- [picgo-plugin-image-resize](https://github.com/yuanhaofen/picgo-plugin-image-resize): 缩放上传的图片。

## 图片格式插件

- [picgo-plugin-webp](https://github.com/lyswhut/picgo-plugin-webp): 将上传的图片转换为 WebP 格式。

## 图床插件

- [picgo-plugin-tcyun](https://github.com/JuniorTour/picgo-plugin-tcyun): 支持腾讯云 COS 图床。
- [picgo-plugin-alioss](https://github.com/liangyuxuan001/picgo-plugin-alioss): 支持阿里云 OSS 图床。
- [picgo-plugin-github-plus](https://github.com/zWingz/picgo-plugin-github-plus): 支持 GitHub 图床。

## 水印插件

- [picgo-plugin-watermark](https://github.com/BeijiYang/picgo-plugin-watermark): 为上传的图片添加水印。

## 复制链接插件

- [picgo-plugin-copy-link](https://github.com/Orz-FLow/picgo-plugin-copy-link): 上传完成后自动将图片链接复制到剪切板。

## 总结

以上是一些常用的 PicGo 插件，可以根据自己的需求进行选择和安装。这些插件可以扩展 PicGo 的功能，让其更加方便实用。
