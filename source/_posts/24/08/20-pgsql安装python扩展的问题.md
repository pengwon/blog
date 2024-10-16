---
title: PostgreSQL安装Python扩展的问题
date: 2024-08-20 11:24:53
categories:
tags:
    - 工具
---


## 问题描述

Windows上PostgreSQL在安装Python扩展时，遇到以下问题：

```sql
postgres=# CREATE EXTENSION plpython3u;

-- 输出：
错误:  无法加载库 "D:/Program Files/PostgreSQL/14/lib/plpython3.dll": The specified module could not be found.
```

## 解决方法

这个问题应该是由于安装时没有勾选扩展选项导致的，解决方法如下：

在系统中找到`python39.dll`这个文件，然后复制到`C:\Windows\System32`目录下，然后重新执行安装命令即可：

```sql
postgres=# CREATE EXTENSION plpython3u;

-- 输出：
CREATE EXTENSION
```

> 注意：
> 
> 具体要找哪个版本的python动态链接库文件，可能需要尝试，这个应该跟安装的PostgreSQL版本有关。我这里是PostgreSQL 14，复制`python39.dll`解决问题。更新的版本可能需要对应更高版本的python动态链接库文件。
