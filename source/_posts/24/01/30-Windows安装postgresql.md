---
title: windows安装postgresql遇到的坑
date: 2024-01-30 11:24:53
categories:
tags:
    - windows
    - postgresql
---


最近在尝试做元器件信息系统，需要用到数据库，之前学习python时一直被安利postgresql，所以就打算用postgresql。其实对初级用户来说，mysql、postgresql都差不多，用不到什么高级功能，完全个人偏好。

## 1.下载安装包

[PostgreSQL下载地址](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)

找到Windows平台安装包下载即可。

## 2.安装

按照安装向导安装，数据库安装一切都很顺利，问题出在扩展安装上。

其实我似乎用不到PostGIS Bundle，但是安装时我还是勾选了，安装过程也没有提示错误，就这样安装完成了。但是当我点击 PostGIS 图标时就开始报错了。

![PostGIS](https://imgs.boringhex.top/blog/20240130221244.png)

<!-- more -->

## 什么是PostGIS

PostGIS 是一个开源的地理信息系统（GIS）扩展，它允许在 PostgreSQL 数据库中存储地理空间数据（即地理空间对象）。PostGIS 提供了一组空间和地理分析的函数，使 PostgreSQL 数据库能够存储、查询和处理地理信息数据。

以下是 PostGIS 的主要特点和功能：

1. **空间数据存储：** PostGIS 允许将空间数据（如点、线、多边形等）存储为 PostgreSQL 数据库中的地理空间对象。

2. **空间索引：** PostGIS 提供了有效的空间索引，以加速对地理数据的查询和分析。

3. **地理空间查询：** 使用 SQL 查询语言，可以执行各种地理空间查询，例如点在多边形内的查询、两个几何对象之间的距离计算等。

4. **地理处理函数：** PostGIS 提供了大量的地理处理函数，包括距离计算、空间关系判断、几何对象的变换等。

5. **支持标准地理空间数据格式：** PostGIS 支持多种标准的地理空间数据格式，如 Well-Known Text (WKT) 和 Well-Known Binary (WKB)。

6. **空间分析功能：** PostGIS 提供了丰富的空间分析功能，包括缓冲区分析、交集分析、合并分析等。

7. **地图投影支持：** 支持不同的地图投影，以便在不同的地理坐标系中存储和分析数据。

PostGIS 极大地扩展了 PostgreSQL 数据库的地理信息处理能力，使其成为一个强大的地理信息系统。由于其开源性质，它被广泛用于各种应用领域，包括地理信息系统、地理空间分析、位置智能服务等。

## 问题1：dll缺失

打开时会提示：无法打开PostGIS PostGIS Bundle 3 for PostgreSQL x64 15 Shapefile and DBF Loader Exporter 弹出窗口 找不到libcrypto-3 postgis libcrypto libssl-3-x64等问题

![找不到dll](https://imgs.boringhex.top/blog/20240130221942.png)

**解决方法：**

打开PostgreSQL安装目录下的Bin文件夹 查找缺少的文件 将其复制到 postgisgui 下

![复制 粘贴](https://imgs.boringhex.top/blog/20240130222157.png)

之后再打开PostGIS就不会报错了。

![打开PostGIS](https://imgs.boringhex.top/blog/20240130222445.png)

## 问题2：没有空间数据库模板

安装完成后没有空间数据库template_postgis模板，无法以模板创建空间数据库

![缺少模板](https://imgs.boringhex.top/blog/20240130222748.png)

**解决方法：**

新建数据库后，右键查询工具输入：

```sql
-- Enable PostGIS (includes raster)
CREATE EXTENSION postgis;
-- Enable Topology
CREATE EXTENSION postgis_topology;
-- Enable PostGIS Advanced 3D 
-- and other geoprocessing algorithms
-- sfcgal not available with all distributions
CREATE EXTENSION postgis_sfcgal;
-- fuzzy matching needed for Tiger
CREATE EXTENSION fuzzystrmatch;
-- rule based standardizer
CREATE EXTENSION address_standardizer;
-- example rule data set
CREATE EXTENSION address_standardizer_data_us;
-- Enable US Tiger Geocoder
CREATE EXTENSION postgis_tiger_geocoder;
```

执行语句后，刷新数据库，可以看到扩展中出现postgis

![postgis扩展](https://imgs.boringhex.top/blog/20240130223209.png)
