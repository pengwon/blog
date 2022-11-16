---
title: 一次惊险的git操作
date: 2022-11-15 14:03:34
tags:
    - git
---

前两天写完一篇文章，但是还没有发布，因为一直在调试hexo和next-theme的配置。然后就发生了一件很悲催的事情，执行了`git add .`，正准备`git commit`时，发现有很多文件不想加到git仓库，也就是在工作区执行了add，但是还没有commit，此时脑袋一热，执行了`git reset --hard HEAD`，然后就悲剧了，工作区的修改全没了。

脑袋嗡的一下，敲完回车的那一刹那我就感觉哪里不对了，但为时已晚。

<!-- more -->

先说一下如果想恢复缓存区里工作区的修改，其实很简单，`git status`的提示也很清楚：

``` powershell
❯ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   _config.next.yml
        modified:   _config.yml
        new file:   source/_posts/一次危险的git操作.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   source/_posts/一次危险的git操作.md
```

注意，这里我已经在`git add`后又修改了`一次危险的git操作.md`这个文件，这里的修改当然包括删除操作。

此时只需要`git restore .`，就可以把工作区恢复到上次`add`时的状态，即取消当前工作区的修改。

而如果加上`--staged`参数，则把缓存区的内容恢复到工作区，注意此时工作区的修改不会被取消，即如果工作区删除了文件，那执行`git restore --staged`是无法恢复的。

要是`git add .`后删除了文件，然后又执行了`git reset --hard HEAD`，那就无法简单恢复了。

如何能恢复呢？

我在网上一通搜索，某乎上[这个帖子](https://www.zhihu.com/question/27467673)还是提供了很好的参考，但不是很详细。

大致上的解决方法有两个：`git reflog`，`git fsck --lost-found`。

1. `git reflog`

``` powershell
❯ git reflog
ffe9822 (HEAD -> main) HEAD@{0}: reset: moving to HEAD
ffe9822 (HEAD -> main) HEAD@{1}: reset: moving to HEAD
ffe9822 (HEAD -> main) HEAD@{2}: commit: mofify: 修改配置文件
a8de715 (origin/main) HEAD@{3}: commit: add: 添加文章
e3bb4d8 HEAD@{4}: reset: moving to HEAD
e3bb4d8 HEAD@{5}: commit: modify _config
d7e58c5 HEAD@{6}: Branch: renamed refs/heads/master to refs/heads/main
d7e58c5 HEAD@{8}: commit (initial): init commit
```

这个命令，我的理解可以解决丢步的情况，比如reset到了更早的commit，把那之后的commit给整没了，此时可以用这个命令恢复。

2. `git fsck --lost-found`

``` powershell
❯ git fsck --lost-found
Checking object directories: 100% (256/256), done.
dangling tree 051912e095bb9d741aeec78c41afc051c6bb98db
dangling blob 8ef0c755cba8a88e113c90f378902f0b7914c32c
dangling blob 3cd9b74819ac88752c9e1a6b3c95b71e0860cdb0
dangling blob dcc06571a9d69efea1046f3f7d2b7e9303aa5509
dangling blob ee5beaf30617b77b02db09b71d7eca0039c8f76c
dangling blob f857e88e2aed2cf87dc877ebe3c29b53fd21aaad
```

看到这个，问题基本就解决了。接下来就是把对应文件进行恢复。

``` powershell
❯ git show 8ef0c755cba8a88e113c90f378902f0b7914c32c
---
title: 一次危险的git操作
date: 2022-11-15 14:03:34
tags:
    - git
---

前两天写完一篇文章，但是还没有发布，因为一直在调试hexo和next-theme的配置。然后就发生了一件很悲催的事情，执行了`git add .`，正准备`git commit`时，发现有很多文件不想加到git仓库，也就是在工作区执行了add，但是还没有commit，此时脑袋一热，执行了`---
title: 一次危险的git操作
date: 2022-11-15 14:03:34
tags:
    - git
---

前两天写完一篇文章，但是还没有发布，因为一直在调试hexo和next-theme的配置。然后就发生了一件很悲催的事情，执行了`git add .`，正准备`git commit`时，发现有很多文件不想加到git仓库，也就是在工作区执行了add，但是还没有commit，此时脑袋一热，执行了`git reset --hard HEAD`，然后就悲剧了，工作区的修改全没了。

脑袋嗡的一下，敲完回车的那一刹那我就感觉哪里不对了，但为时已晚。

先说一下如果想恢复缓存区里工作区的修改，其实很简单，`git status`的提示也很清楚：

``` powershell
❯ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   _config.next.yml
        modified:   _config.yml
        new file:   source/_posts/一次危险的git操作.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
:
```

不同的文件对应不同的文件和状态，找到自己需要的状态恢复即可。

我当时用这个命令并没有起作用，转而使用的另一个方法。git的所有操作都是增量操作，除非特别刻意地去删除，否则要想让一个文件消失得无影无踪还是有点儿困难的。这个方法是直接去`.git/objects`目录查找。参考了[这篇文章](https://www.jianshu.com/p/311c5e33c705)。

如果用git bash的话可以使用`find`命令进行查找：`find .git/objects -type f | xargs ls -lt | sed 30q`，这条命令输出最近`add`的30个文件，可以根据情况设置相应的值。**Windows中没有`find`命令，需要在git bash中执行**。

我使用的是powershell，用[`fd`](https://github.com/sharkdp/fd)命令替代`find`：

``` powershell
❯ fd . '.\.git\objects' -t f --changed-within '2022-11-15'
.git\objects\09\2c9306cd5b2761048b8020a0802a6a4a068ed7
.git\objects\3c\d9b74819ac88752c9e1a6b3c95b71e0860cdb0
.git\objects\6a\36a8a9c4b02c92684bc67ed65cdd12ee055de3
.git\objects\8a\c69f0b1d0032ace1e6e18f70d2bfc103591560
.git\objects\8e\f0c755cba8a88e113c90f378902f0b7914c32c
.git\objects\a0\37eb5a4333bdcc625f9ea4e5df13b0487e470b
.git\objects\b8\36d4b7235fe833fee00ea9278082d5ba4354f7
.git\objects\dc\c06571a9d69efea1046f3f7d2b7e9303aa5509
.git\objects\ee\5beaf30617b77b02db09b71d7eca0039c8f76c
.git\objects\f8\57e88e2aed2cf87dc877ebe3c29b53fd21aaad
.git\objects\ff\e9822967eac8c6e6845fbc133b18a13d11d446
```

从这里可以看出结果跟上面的`git fsck --lost-found`是基本一致的。这个方法需要把结果处理下：.git\objects\09\2c9306cd5b2761048b8020a0802a6a4a068ed7 → 092c9306cd5b2761048b8020a0802a6a4a068ed7，可以使用脚本，也可以重定向到文件然后用编辑器批量修改的方法，然后再用`git show`就可以了。

如果文件比较多的话，可以编写脚本

``` powershell
$f = Get-Content .\files.txt  # 假定上一步将结果重定向到files.txt，并且已经批量编辑为需要的格式
foreach ($l in $f){git show $l | out-file x$l.txt}
```

这里用powershell有一个大坑，就是如果文件中有中文的话，得到的文本文件会有乱码。用git bash和cmd则没有这个问题。问题是很容易绕过去解决，但是不找到原因怎么睡得着？因为`git show`的输出是没有问题的，所以想来想去，觉得问题应该就是出在powershell的默认编码上，后来一通搜索，在github这个[issue](https://github.com/PowerShell/PowerShell/issues/14945)得到了确认。

``` powershell
❯ [console]::inputencoding;[console]::outputencoding

EncodingName      : Chinese Simplified (GB2312)
WebName           : gb2312
HeaderName        : gb2312
BodyName          : gb2312
Preamble          :
WindowsCodePage   :
IsBrowserDisplay  :
IsBrowserSave     :
IsMailNewsDisplay :
IsMailNewsSave    :
IsSingleByte      : False
EncoderFallback   : System.Text.InternalEncoderBestFitFallback
DecoderFallback   : System.Text.InternalDecoderBestFitFallback
IsReadOnly        : True
CodePage          : 936

EncodingName      : Chinese Simplified (GB2312)
WebName           : gb2312
HeaderName        : gb2312
BodyName          : gb2312
Preamble          :
WindowsCodePage   :
IsBrowserDisplay  :
IsBrowserSave     :
IsMailNewsDisplay :
IsMailNewsSave    :
IsSingleByte      : False
EncoderFallback   : System.Text.InternalEncoderBestFitFallback
DecoderFallback   : System.Text.InternalDecoderBestFitFallback
IsReadOnly        : False
CodePage          : 936
```

对，问题就出在这，应该跟安装powershell时系统语言有关系。要解决这个问题，建议还是不要修改默认配置，只修改当前控制台的变量就好（只在当前终端生效）。

``` powershell
❯ [console]::inputencoding = [console]::outputencoding = [System.Text.Encoding]::UTF8
❯ [console]::inputencoding;[console]::outputencoding

Preamble          :
BodyName          : utf-8
EncodingName      : Unicode (UTF-8)
HeaderName        : utf-8
WebName           : utf-8
WindowsCodePage   : 1200
IsBrowserDisplay  : True
IsBrowserSave     : True
IsMailNewsDisplay : True
IsMailNewsSave    : True
IsSingleByte      : False
EncoderFallback   : System.Text.EncoderReplacementFallback
DecoderFallback   : System.Text.DecoderReplacementFallback
IsReadOnly        : False
CodePage          : 65001

Preamble          :
BodyName          : utf-8
EncodingName      : Unicode (UTF-8)
HeaderName        : utf-8
WebName           : utf-8
WindowsCodePage   : 1200
IsBrowserDisplay  : True
IsBrowserSave     : True
IsMailNewsDisplay : True
IsMailNewsSave    : True
IsSingleByte      : False
EncoderFallback   : System.Text.EncoderReplacementFallback
DecoderFallback   : System.Text.DecoderReplacementFallback
IsReadOnly        : False
CodePage          : 65001
```

这时再执行前面的脚本，输出应该就正常了。如果文件不多，直接复制一份出来直接对照文件内容改回原来的文件名也行。

总结
1. git仓库中还是不要做`shift+del`这种操作
2. `git reset --hard`要谨慎
3. 遇到问题别慌，耐心找方法
4. git的3个区还是要加深理解
5. 学无止境

