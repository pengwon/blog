---
title: git中文乱码
date: 2023-01-30 21:34:01
categories:
tags:
  - git
  - powershell
  - windows
---

执行`git status`时，如果路径或文件名中有中文，会显示为八进制乱码，很不方便：

``` powershell
❯ git status
On branch develop
Your branch is up to date with 'origin/develop'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        "\344\270\255\346\226\207.txt"
```

可以通过以下命令解决：

``` powershell
❯ git config --global core.quotepath false

# 再执行 git status 就没问题了
❯ git status
On branch develop
Your branch is up to date with 'origin/develop'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        中文.txt
```

git[文档](https://git-scm.com/docs/git-config#Documentation/git-config.txt-corequotePath)对这个配置的说明：

> core.quotePath
> 
> Commands that output paths (e.g. ls-files, diff), will quote "unusual" characters in the pathname by enclosing the pathname in double-quotes and escaping those characters with backslashes in the same way C escapes control characters (e.g. \t for TAB, \n for LF, \\ for backslash) or bytes with values larger than 0x80 (e.g. octal \302\265 for "micro" in UTF-8). If this variable is set to false, bytes higher than 0x80 are not considered "unusual" any more. Double-quotes, backslash and control characters are always escaped regardless of the setting of this variable. A simple space character is not considered "unusual". Many commands can output pathnames completely verbatim using the -z option. The default value is true.

路径名中包含"不寻常"字符（猜测就是非ascii字符）时，输出路径的命令（例如 ls-files、diff，当然也有status）会用双引号将路径名引起来，并用与`C`语言转义控制字符相同的方式转义那些带反斜杠的字符（例如 "\t"表示TAB，"\n"表示LF，"\\"表示反斜杠），或大于 `0x80` 的字节码（例如，八进制`\302\265`表示UTF-8编码的"[micro](https://www.compart.com/en/unicode/U+00B5)"符号，其utf8编码为"U+00B5"）。如果此变量设置为 false，则字节码大于`0x80`不再被认为是“不寻常的”。双引号、反斜杠和控制字符始终被转义，而无论此变量的设置。一个简单的空格字符是不被认为“不寻常的”。许多命令可以使用`-z`选项完整逐字输出路径名。该变量默认值是`true`。

可以通过python简单验证：

``` python
>>> 'µ'.encode('utf8')
b'\xc2\xb5'

# 下面按字节展开
>>> oct(0xc2)
'0o302'
>>> oct(0xb5)
'0o265'

>>> "中文.txt".encode('utf8')
b'\xe4\xb8\xad\xe6\x96\x87.txt'
>>> oct(0xe4)
'0o344'
>>> oct(0xb8)
'0o270'
>>> oct(0xad)
'0o255'
>>> oct(0xe6)
'0o346'
>>> oct(0x96)
'0o226'
>>> oct(0x87)
'0o207'
```
