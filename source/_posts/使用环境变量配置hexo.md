---
title: 使用环境变量配置hexo
date: 2023-02-17 12:20:27
categories:
tags:
  - hexo
---

在部署hexo博客的过程中，遇到这样一个问题：hexo的部署插件都是在配置文件中配置密钥，而我是使用GitHub Actions进行部署，配置文件也放在仓库中，就会导致部署密钥泄露，所以目前是使用了腾讯的COS部署插件实现，它可以在GitHub Actions中直接调用。

但还是想知道如何在hexo的层面上解决，所以在[hexo仓库](https://github.com/hexojs/hexo)提交了一个[issue](https://github.com/hexojs/hexo/issues/5154)。

本来以为hexo目前还不支持，结果作者回复可以使用`config`命令来实现：

``` sh
export THEME=next
hexo config theme $THEME
```

我在hexo网站有搜了下文档，没有相关说明。但是这个命令可以使用：

``` powershell
❯ hexo -h  
INFO  Validating config
Usage: hexo <command>

Commands:
  clean     Remove generated files and cache.
  config    Get or set configurations.
  deploy    Deploy your website.
  generate  Generate static files.
  help      Get help on a command.
  init      Create a new Hexo folder.
  list      List the information of the site
  migrate   Migrate your site from other system to Hexo.
  new       Create a new post.
  publish   Moves a draft post from _drafts to _posts folder.
  render    Render files with renderer plugins.
  server    Start the server.
  version   Display version information.

Global Options:
  --config  Specify config file instead of using _config.yml
  --cwd     Specify the CWD
  --debug   Display all verbose messages in the terminal
  --draft   Display draft posts
  --safe    Disable all plugins and scripts
  --silent  Hide output on console

For more help, you can use 'hexo help [command]' for the detailed information
or you can check the docs: http://hexo.io/docs/

❯ hexo help config
INFO  Validating config
Usage: hexo config [name] [value]

Description:
Get or set configurations.

Arguments:
  name   Setting name. Leave it blank if you want to show all configurations.
  value  New value of a setting. Leave it blank if you just want to show a single configuration.
```

获取`theme`的配置：

``` powershell
❯ hexo config theme
INFO  Validating config
next
```

如果需要设置某一项的子项，比如`related_posts`中的`enable`：

``` powershell
❯ hexo config related_posts.enable
INFO  Validating config
true
```
