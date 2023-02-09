---
title: ai赋能powershell
date: 2023-02-09 17:18:10
categories:
tags:
  - powershell
  - ai
  - chatgpt
---

最近chatgpt非常火爆，获得超高曝光度的同时，也让大家对ai和ai工具有了新的认识。关于chatgpt，可以参考这篇文章。

今天主要推荐一个可以与powershell集成的ai工具，其后端也是openai的服务，可以有效提高工作效率。

## [PowerShellAI](https://github.com/dfinke/PowerShellAI)

![](https://imgs.boringhex.top/blog/20230209175553.png)

<!-- more -->

### 安装

打开`powershell`，运行：

``` powershell
Install-Module -Name PowerShellAI
```

这里可能会遇到找不到模块提供者的问题：

``` powershell
Install-Package: Unable to find module providers (PowerShellGet).
```

可以参考[这篇文章](https://blog.boringhex.top/posts/b9c6bc459258/)解决。

安装成功后，从 https://beta.openai.com/account/api-keys 创建和获取OpenAI API密钥，然后设置`$env:OpenAIKey`环境变量：

``` powershell
# 临时设置，仅对当前会话有效
$env:OpenAIKey="你的OpenAI API密钥"

# 永久设置
[Environment]::SetEnvironmentVariable('OpenAIKey', '你的OpenAI API密钥', 'User')
```

这样就可以愉快地玩耍了。

### 使用

目前支持以下命令：

1. `Disable-AIShortCutKey` - 禁用生成文本的快捷键`ctrl+g`
2. `Enable-AIShortCutKey` - 使能`ctrl+g`快捷键
3. `Get-GPT3Completion` - 从OpenAI GPT-3 API获取自动生成的文本
4. `Get-DalleImage` - 从OpenAI DALL-E API获取自动生成的图片
5. `ai` - 实验性的可以用于管道的ai功能
6. `copilot` - 生成代码提示，并询问是否运行
7. `Set-DalleImageAsWallpaper` - 获取图片后设为壁纸

### 示例

> `Get-GPT3Completion`可以使用别名`gpt`

1. 以`json`格式列出行星名

``` powershell
❯ Get-GPT3Completion "list of planets only names as json"

[
    "Mercury",
    "Venus",
    "Earth",
    "Mars",
    "Jupiter",
    "Saturn",
    "Uranus",
    "Neptune"
]
```

2. `xml`

``` powershell
❯ Get-GPT3Completion "list of planets only names as xml"


<?xml version="1.0" encoding="UTF-8"?>
<planets>
  <planet>Mercury</planet>
  <planet>Venus</planet>
  <planet>Earth</planet>
  <planet>Mars</planet>
  <planet>Jupiter</planet>
  <planet>Saturn</planet>
  <planet>Uranus</planet>
  <planet>Neptune</planet>
</planets>
```

3. `markdown`

``` powershell

❯ Get-GPT3Completion "first 5 US presidents and terms as markdown table"


| President | Term |
|----------|------|
| George Washington | 1789-1797 |
| John Adams | 1797-1801 |
| Thomas Jefferson | 1801-1809 |
| James Madison | 1809-1817 |
| James Monroe | 1817-1825 |
```

复制到当前文章的效果：

| President | Term |
|----------|------|
| George Washington | 1789-1797 |
| John Adams | 1797-1801 |
| Thomas Jefferson | 1801-1809 |
| James Madison | 1809-1817 |
| James Monroe | 1817-1825 |

4. `ai`命令

`ai`命令支持piping和prompting，这对将不同命令串起来非常有用：

``` powershell
❯ ai "list of planets only names as json" | ai 'convert to  xml' | ai 'convert to  powershell'


[xml]$xml = @"
<?xml version="1.0" encoding="UTF-8"?>
<Planets>
    <Planet>Mercury</Planet>
    <Planet>Venus</Planet>
    <Planet>Earth</Planet>
    <Planet>Mars</Planet>
    <Planet>Jupiter</Planet>
    <Planet>Saturn</Planet>
    <Planet>Uranus</Planet>
    <Planet>Neptune</Planet>
</Planets>
"@

$xml.Planets.Planet
```

与`git`一起使用：

``` powershell
❯ git status | ai "create a detailed git message"


Commit message:
Update posts related to STM32 and PowerShell, add two new posts about PowerShell and AI.
```
