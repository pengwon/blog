---
title: Altium Designer元器件库ADLib上线试用了
date: 2024-05-21 11:24:53
categories:
tags:
    - ad/altium designer
    - ecad/eda
---

# Altium Designer元器件库ADLib上线试用了

本来想准备充分了，至少将元器件数量扩充到1000+再上线，但想来想去，还是应该趁着规模不大时上线试用，以便及时发现问题，及时修复，在实践中不断迭代更新，逐步完善，添加功能特性。所以，ADLib元器件库现在上线试用了，欢迎大家试用拍砖，提出宝贵意见和建议。

安装前可以先阅读 [这篇文章](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651482479&idx=1&sn=d79a11fe80da1f39fe1e46ea2c6870a0&chksm=84ad7facb3daf6ba008c8f7219550a07e711cfb7742180b865ad2c836176ce6982926200e28f#rd) 了解一些背景知识。

## 使用说明

### 1. 安装PostgreSQL数据库驱动

ADLib元器件库使用PostgreSQL数据库存储元器件信息，所以需要安装PostgreSQL数据库驱动，可以到 [PostgreSQL官网](https://odbc.postgresql.org/) 下载对应的驱动，建议安装64位版。访问官网有困难的同学可以到百度网盘下载：

链接: https://pan.baidu.com/s/1DnWgRIRjggwleEUL5EnFAw?pwd=ty4h 提取码: ty4h

安装13.2版本就可以。

<!-- more -->

安装好ODBC驱动后，到Windows的数据源管理器中添加ODBC数据源：

![ODBC数据源](https://imgs.boringhex.top/blog/20240327173633.png)

我安装的是64位驱动，所以这里打开的是64位的ODBC管理器：

![添加数据源](https://imgs.boringhex.top/blog/20240327173849.png)

打开后选择添加，然后选择刚才安装的数据库对应的驱动：

![选择数据库驱动](https://imgs.boringhex.top/blog/20240327174100.png)

填写数据源名称和描述（可以不修改，保持默认），配置连接参数，然后点击测试连接，如果连接成功，点击确定完成添加。

![配置数据源名称和连接参数](https://imgs.boringhex.top/blog/20240327174552.png)

至此，数据源就添加好了。

### 2. 下载元器件库

AD数据库类型的元器件库，元器件信息存储在数据库中，元器件符号和封装依然使用对应库文件。

到[GitHub](https://github.com/boringhex-top/OpenECADLib)下载元器件库文件：

![下载元器件库](https://imgs.boringhex.top/blog/20240521113407.png)

这里下载zip压缩包或使用git clone都可以，建议使用git clone，这样可以方便后续更新。

对git不熟悉的可以参考 [Pro Git](https://git-scm.com/book/zh/v2)。

不方便访问GitHub的小伙伴可以`clone`以下地址：

```powershell
git clone https://github.boringhex.top/github.com/boringhex-top/OpenECADLib.git
```

当然也可以用上面截图中的加速器。

### 3. 配置ADLib元器件库

下载好元器件库后，建议将元器件库存放在一个固定的位置，方便后续更新。

将元器件库中的`example.std-lib.DbLib`修改文件名为`std-lib.DbLib`，然后用Altium Designer打开：

![构建数据库连接](https://imgs.boringhex.top/blog/20240521120755.png)

如果前面的ODBC数据源名称跟我前面的示例一致，这里只修改`User ID`和`Password`即可。

如果ODBC数据源名称不一致，可以点击`Build`修改数据源配置，根据向导选择对应的ODBC数据源。

然后点击`Connect`测试连接，连接成功后点击`Ctrl + S`保存DbLib文件。

### 4. 使用ADLib元器件库

新建工程，然后安装基于文件的库：

![安装库](https://imgs.boringhex.top/blog/20240521121821.png)

![安装ADLib](https://imgs.boringhex.top/blog/20240521122003.png)

可以全局安装这个库，这样在所有工程中都可以使用。

![元器件面板](https://imgs.boringhex.top/blog/20240521132018.png)

### 5. 更新元器件库

在元器件库目录下，使用`git pull`命令更新库文件：

```powershell
git pull
```

## 如何获取账号

目前ADLib元器件库还在试用阶段，资源有限，赞助后我会私信开通账号，1块2块不嫌少，10元20元不嫌多😀

赞助5元以上还可以获得一个月的c科l学a上s网h订阅。

## 申请新器件

可以通过 [申请新器件](https://github.com/boringhex-top/OpenECADLib/issues/new) 提交新器件申请，请尽可能详细提供器件信息，至少应包括Datasheet链接或附件。

赞助的同学提交的新器件申请会在24小时内更新，每人每天不超过5个。

## 参与维护

如果你对这个项目感兴趣，欢迎加入，一起改进和完善它。

识别下方二维码可以加入微信交流群：

![微信交流群](https://imgs.boringhex.top/blog/20240521135953.png)
