---
title: Hexo安装和配置
tags:
  - hexo
  - scoop
  - powershell
date: 2022-11-15 11:25:54
---


## 安装

Hexo的安装比较简单，其中不太友好的点就是nodejs的安装。这里强烈推荐[Scoop](https://scoop.sh/)！

我是参照中文版[官方文档](https://hexo.io/zh-cn/docs/)进行安装，文中要求nodejs和git，git早早就安装了，安装nodejs时遇到一点小问题，就是nodejs版本太多、更新太快。

<!-- more -->

### [Git](https://git-scm.com/)

- Win：建议通过[winget](https://learn.microsoft.com/zh-CN/windows/package-manager/winget/)，微软官方的包管理工具，现在win10和win11应该都已经内置。如果安装太慢，可以使用[国内镜像](https://registry.npmmirror.com/binary.html?path=git-for-windows/)下载安装。当然了，也可以使用前面推荐的`scoop`进行安装。
- Mac：[Homebrew](http://mxcl.github.com/homebrew/)、[MacPorts](http://www.macports.org/)或下载[安装程序](http://sourceforge.net/projects/git-osx-installer/)。
- Linux：用Linux的用户这一步应该就不用说了。。

> **Mac用户**
> 
> 如果安装安装过程遇到问题，可以先安装Xcode，安装后启动并进入Preferences -> Download -> Command Line Tools -> Install 安装命令行工具。

### [Node.js](https://nodejs.org/zh-cn/)

下载官方[安装程序](https://nodejs.org/zh-cn/download/)，可以用[国内镜像](https://registry.npmmirror.com/binary.html?path=node/)加速下载。版本实在太多了，建议`scoop install nodejs-lts`进行安装，省心省力。

### Hexo

进行到这里，前戏结束，进入正题。安装完nodejs之后，最重要的事情就是给nodejs的包管理工具npm换源，默认的源很不稳定。

- 单次使用：

``` powershell
npm install -g hexo-cli --registry=http://registry.npmmirror.com
```

- 永久：

``` powershell
npm config set registry http://registry.npmmirror.com
npm install -g hexo-cli
```

建议直接永久替换为国内源。

也可以使用阿里的cnpm命令行工具代替默认的npm：

``` powershell
npm install -g cnpm --registry=http://registry.npmmirror.com
```

检查是否安装成功：

``` powershell
cnpm -v
```

安装成功后，再安装依赖包的时候把`npm`命令替换成`cnpm`就可以了：

``` powershell
cnpm install -g hexo-cli
```

官方文档中写的局部安装hexo包没太大意义，大家酌情考虑。建议安装最新版本的Hexo。

## 配置

安装成功后，配置相对来说简单但繁琐。正常情况下使用默认配置就可以运行起来。hexo更新也比较快，这里写的配置不一定一直适用，我目前安装的最新版是6.3.0，建议还是参照[官方文档](https://hexo.io/zh-cn/docs/configuration)进行配置，修改前可以备份下当前配置。也可以另存一个配置文件，然后用`--config`参数指定自定义的配置文件。例如：

``` powershell
# 用 'custom.yml' 代替 '_config.yml'
hexo server --config custom.yml

# 使用 'custom.yml' 和 'custom2.json'，优先使用 'custom3.yml'，然后是 'custom2.json'
hexo generate --config custom.yml,custom2.json,custom3.yml
```

这里重点说一下关于主题的配置问题，传统上通常在主题目录下有一个`_config.yml`文件，可以在这里进行配置。但是hexo的主题通常是一个独立项目，这样更新主题后，配置文件可能就会冲突或被覆盖，所以现在hexo推荐采用代替主题配置文件的方法。

- 使用配置文件中的`theme_config`

> 该特性自Hexo 2.8.2起提供

``` yml
# _config.yml
theme: "my-theme"
theme_config:
  bio: "My awesome bio"
  foo:
    bar: 'a'
```

``` yml
# themes/my-theme/_config.yml
bio: "Some generic bio"
logo: "a-cool-image.png"
  foo:
    baz: 'b'
```

则最终主题配置是：

``` yml
{
  bio: "My awesome bio",
  logo: "a-cool-image.png",
  foo: {
    bar: "a",
    baz: "b"
  }
}
```
从中也可以看出hexo中配置的优先顺序。

- 使用独立的`_config.[theme].yml`文件

> 该特性自Hexo 5.0.0起提供

独立的主题配置文件应放在站点根目录下，支持`yml`和`json`格式，需要配置站点`_config.yml`文件中的`theme`以供hexo寻找相应配置文件。

## 主题

主题设置见仁见智，自己喜欢就好。本站选用的是[Next主题](https://theme-next.js.org/)。github上有两个Next主题的仓库，可能是迁移到8.0之后重新建库了。具体配置直接参照[官方文档](https://theme-next.js.org/docs/)就可以，目前还没发现什么坑。
