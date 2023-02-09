---
title: 非常好用的http静态文件服务器
date: 2023-02-09 14:51:39
categories:
tags:
  - 文件服务
---

推荐几个非常好用的http静态文件服务程序，小巧、好用。

## [miniserve](https://github.com/svenstaro/miniserve)

> a CLI tool to serve files and dirs over HTTP
> For when you really just want to serve some files over HTTP right now!

miniserve采用`rust`编写，是一个小型的，独立的跨平台CLI工具，允许您仅获取二进制文件并通过HTTP提供一些文件。 有时这只是一种比正确做事更实用、更快捷的方法。

实例截图：

![](https://imgs.boringhex.top/blog/20230209150153.png)

在局域网内作为团队文件归档工具非常方便。

### 特性

- 易于使用
- 正常工作：开箱即用的正确 MIME 类型处理
- 单个二进制插入，无需额外的依赖项
- 使用用户名和密码（以及散列密码）进行身份验证支持
- 超级快速和高度并行（感谢 Rust 和 Actix)
- 文件夹下载（动态压缩为 或.tar.gz.zip)
- 文件上传
- 目录创建
- 漂亮的主题（支持浅色和深色主题）
- 扫描二维码快速访问
- Shell自动补全
- 合理和安全的默认值
- TLS（用于支持的体系结构）
- 支持像在 GitHub 上一样 README.md 渲染

### 安装

``` powershell
scoop install miniserve
```

其它系统的安装请参考[文档](https://github.com/svenstaro/miniserve#how-to-install)。

### 使用

``` powershell
❯ miniserve -h
miniserve 0.22.0
Sven-Hendrik Haase <svenstaro@gmail.com>, Boastful Squirrel <boastful.squirrel@gmail.com>
For when you really just want to serve some files over HTTP right now!

USAGE:
    miniserve.exe [OPTIONS] [--] [PATH]

ARGS:
    <PATH>    Which path to serve

OPTIONS:
    -a, --auth <AUTH>                               Set authentication. Currently supported formats: username:password, username:sha256:hash, username:sha512:hash (e.g. joe:123, joe:sha256:a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3)
    -c, --color-scheme <COLOR_SCHEME>               Default color scheme [default: squirrel] [possible values: squirrel, archlinux, zenburn, monokai]
    -d, --color-scheme-dark <COLOR_SCHEME_DARK>     Default color scheme [default: archlinux] [possible values: squirrel, archlinux, zenburn, monokai]
    -D, --dirs-first                                List directories first
    -F, --hide-version-footer                       Hide version footer
    -g, --enable-tar-gz                             Enable gz-compressed tar archive generation
    -h, --help                                      Print help information
    -H, --hidden                                    Show hidden files
        --header <HEADER>                           Set custom header for responses
        --hide-theme-selector                       Hide theme selector
    -i, --interfaces <INTERFACES>                   Interface to listen on
        --index <index_file>                        The name of a directory index file to serve, like "index.html"
    -l, --show-symlink-info                         Visualize symlinks in directory listing
    -m, --media-type <MEDIA_TYPE>                   Specify uploadable media types [possible values: image, audio, video]
    -M, --raw-media-type <MEDIA_TYPE_RAW>           Directly specify the uploadable media type expression
    -o, --overwrite-files                           Enable overriding existing files during file upload
    -p, --port <PORT>                               Port to use [default: 8080]
    -P, --no-symlinks                               Hide symlinks in listing and prevent them from being followed
        --print-completions <shell>                 Generate completion file for a shell [possible values: bash, elvish, fish, powershell, zsh]
        --print-manpage                             Generate man page
    -q, --qrcode                                    Enable QR code display
    -r, --enable-tar                                Enable uncompressed tar archive generation
        --random-route                              Generate a random 6-hexdigit route
        --readme                                    Enable README.md rendering in directories
        --route-prefix <ROUTE_PREFIX>               Use a specific route prefix
        --spa                                       Activate SPA (Single Page Application) mode
    -t, --title <TITLE>                             Shown instead of host in page title and heading
        --tls-cert <TLS_CERT>                       TLS certificate to use
        --tls-key <TLS_KEY>                         TLS private key to use
    -u, --upload-files [<ALLOWED_UPLOAD_DIR>...]    Enable file uploading (and optionally specify for which directory)
    -U, --mkdir                                     Enable creating directories
    -v, --verbose                                   Be verbose, includes emitting access logs
    -V, --version                                   Print version information
    -W, --show-wget-footer                          If enabled, display a wget command to recursively download the current directory
    -z, --enable-zip                                Enable zip archive generation
```

为powershell生成自动补全文件：

``` powershell
❯ miniserve --print-completions powershell

using namespace System.Management.Automation
using namespace System.Management.Automation.Language

Register-ArgumentCompleter -Native -CommandName 'miniserve' -ScriptBlock {
    param($wordToComplete, $commandAst, $cursorPosition)

    $commandElements = $commandAst.CommandElements
    $command = @(
        'miniserve'
        for ($i = 1; $i -lt $commandElements.Count; $i++) {
            $element = $commandElements[$i]
            if ($element -isnot [StringConstantExpressionAst] -or
                $element.StringConstantType -ne [StringConstantType]::BareWord -or
                $element.Value.StartsWith('-') -or
                $element.Value -eq $wordToComplete) {
                break
        }
        $element.Value
    }) -join ';'

    $completions = @(switch ($command) {
        'miniserve' {
            [CompletionResult]::new('--index', 'index', [CompletionResultType]::ParameterName, 'The name of a directory index file to serve, like "index.html"')
            [CompletionResult]::new('-p', 'p', [CompletionResultType]::ParameterName, 'Port to use')
            [CompletionResult]::new('--port', 'port', [CompletionResultType]::ParameterName, 'Port to use')
            [CompletionResult]::new('-i', 'i', [CompletionResultType]::ParameterName, 'Interface to listen on')
            [CompletionResult]::new('--interfaces', 'interfaces', [CompletionResultType]::ParameterName, 'Interface to listen on')
            [CompletionResult]::new('-a', 'a', [CompletionResultType]::ParameterName, 'Set authentication. Currently supported formats: username:password, username:sha256:hash, username:sha512:hash (e.g. joe:123, joe:sha256:a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3)')
            [CompletionResult]::new('--auth', 'auth', [CompletionResultType]::ParameterName, 'Set authentication. Currently supported formats: username:password, username:sha256:hash, username:sha512:hash (e.g. joe:123, joe:sha256:a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3)')
            [CompletionResult]::new('--route-prefix', 'route-prefix', [CompletionResultType]::ParameterName, 'Use a specific route prefix')
            [CompletionResult]::new('-c', 'c', [CompletionResultType]::ParameterName, 'Default color scheme')
            [CompletionResult]::new('--color-scheme', 'color-scheme', [CompletionResultType]::ParameterName, 'Default color scheme')
            [CompletionResult]::new('-d', 'd', [CompletionResultType]::ParameterName, 'Default color scheme')
            [CompletionResult]::new('--color-scheme-dark', 'color-scheme-dark', [CompletionResultType]::ParameterName, 'Default color scheme')
            [CompletionResult]::new('-u', 'u', [CompletionResultType]::ParameterName, 'Enable file uploading (and optionally specify for which directory)')
            [CompletionResult]::new('--upload-files', 'upload-files', [CompletionResultType]::ParameterName, 'Enable file uploading (and optionally specify for which directory)')
            [CompletionResult]::new('-m', 'm', [CompletionResultType]::ParameterName, 'Specify uploadable media types')
            [CompletionResult]::new('--media-type', 'media-type', [CompletionResultType]::ParameterName, 'Specify uploadable media types')
            [CompletionResult]::new('-M', 'M', [CompletionResultType]::ParameterName, 'Directly specify the uploadable media type expression')
            [CompletionResult]::new('--raw-media-type', 'raw-media-type', [CompletionResultType]::ParameterName, 'Directly specify the uploadable media type expression')
            [CompletionResult]::new('-t', 't', [CompletionResultType]::ParameterName, 'Shown instead of host in page title and heading')
            [CompletionResult]::new('--title', 'title', [CompletionResultType]::ParameterName, 'Shown instead of host in page title and heading')
            [CompletionResult]::new('--header', 'header', [CompletionResultType]::ParameterName, 'Set custom header for responses')
            [CompletionResult]::new('--print-completions', 'print-completions', [CompletionResultType]::ParameterName, 'Generate completion file for a shell')
            [CompletionResult]::new('--tls-cert', 'tls-cert', [CompletionResultType]::ParameterName, 'TLS certificate to use')
            [CompletionResult]::new('--tls-key', 'tls-key', [CompletionResultType]::ParameterName, 'TLS private key to use')
            [CompletionResult]::new('-h', 'h', [CompletionResultType]::ParameterName, 'Print help information')
            [CompletionResult]::new('--help', 'help', [CompletionResultType]::ParameterName, 'Print help information')
            [CompletionResult]::new('-V', 'V', [CompletionResultType]::ParameterName, 'Print version information')
            [CompletionResult]::new('--version', 'version', [CompletionResultType]::ParameterName, 'Print version information')
            [CompletionResult]::new('-v', 'v', [CompletionResultType]::ParameterName, 'Be verbose, includes emitting access logs')
            [CompletionResult]::new('--verbose', 'verbose', [CompletionResultType]::ParameterName, 'Be verbose, includes emitting access logs')
            [CompletionResult]::new('--spa', 'spa', [CompletionResultType]::ParameterName, 'Activate SPA (Single Page Application) mode')
            [CompletionResult]::new('--random-route', 'random-route', [CompletionResultType]::ParameterName, 'Generate a random 6-hexdigit route')
            [CompletionResult]::new('-P', 'P', [CompletionResultType]::ParameterName, 'Hide symlinks in listing and prevent them from being followed')
            [CompletionResult]::new('--no-symlinks', 'no-symlinks', [CompletionResultType]::ParameterName, 'Hide symlinks in listing and prevent them from being followed')
            [CompletionResult]::new('-H', 'H', [CompletionResultType]::ParameterName, 'Show hidden files')
            [CompletionResult]::new('--hidden', 'hidden', [CompletionResultType]::ParameterName, 'Show hidden files')
            [CompletionResult]::new('-q', 'q', [CompletionResultType]::ParameterName, 'Enable QR code display')
            [CompletionResult]::new('--qrcode', 'qrcode', [CompletionResultType]::ParameterName, 'Enable QR code display')
            [CompletionResult]::new('-U', 'U', [CompletionResultType]::ParameterName, 'Enable creating directories')
            [CompletionResult]::new('--mkdir', 'mkdir', [CompletionResultType]::ParameterName, 'Enable creating directories')
            [CompletionResult]::new('-o', 'o', [CompletionResultType]::ParameterName, 'Enable overriding existing files during file upload')
            [CompletionResult]::new('--overwrite-files', 'overwrite-files', [CompletionResultType]::ParameterName, 'Enable overriding existing files during file upload')
            [CompletionResult]::new('-r', 'r', [CompletionResultType]::ParameterName, 'Enable uncompressed tar archive generation')
            [CompletionResult]::new('--enable-tar', 'enable-tar', [CompletionResultType]::ParameterName, 'Enable uncompressed tar archive generation')
            [CompletionResult]::new('-g', 'g', [CompletionResultType]::ParameterName, 'Enable gz-compressed tar archive generation')
            [CompletionResult]::new('--enable-tar-gz', 'enable-tar-gz', [CompletionResultType]::ParameterName, 'Enable gz-compressed tar archive generation')
            [CompletionResult]::new('-z', 'z', [CompletionResultType]::ParameterName, 'Enable zip archive generation')
            [CompletionResult]::new('--enable-zip', 'enable-zip', [CompletionResultType]::ParameterName, 'Enable zip archive generation')
            [CompletionResult]::new('-D', 'D', [CompletionResultType]::ParameterName, 'List directories first')
            [CompletionResult]::new('--dirs-first', 'dirs-first', [CompletionResultType]::ParameterName, 'List directories first')
            [CompletionResult]::new('-l', 'l', [CompletionResultType]::ParameterName, 'Visualize symlinks in directory listing')
            [CompletionResult]::new('--show-symlink-info', 'show-symlink-info', [CompletionResultType]::ParameterName, 'Visualize symlinks in directory listing')
            [CompletionResult]::new('-F', 'F', [CompletionResultType]::ParameterName, 'Hide version footer')
            [CompletionResult]::new('--hide-version-footer', 'hide-version-footer', [CompletionResultType]::ParameterName, 'Hide version footer')
            [CompletionResult]::new('--hide-theme-selector', 'hide-theme-selector', [CompletionResultType]::ParameterName, 'Hide theme selector')
            [CompletionResult]::new('-W', 'W', [CompletionResultType]::ParameterName, 'If enabled, display a wget command to recursively download the current directory')
            [CompletionResult]::new('--show-wget-footer', 'show-wget-footer', [CompletionResultType]::ParameterName, 'If enabled, display a wget command to recursively download the current directory')
            [CompletionResult]::new('--print-manpage', 'print-manpage', [CompletionResultType]::ParameterName, 'Generate man page')
            [CompletionResult]::new('--readme', 'readme', [CompletionResultType]::ParameterName, 'Enable README.md rendering in directories')
            break
        }
    })

    $completions.Where{ $_.CompletionText -like "$wordToComplete*" } |
        Sort-Object -Property ListItemText
}
```

将上面输出的内容追加到powershell的配置文件中：

``` powershell
miniserve --print-completions powershell >> $profile
```

serve 一个目录：

``` powershell
miniserve http/
```

serve 单个文件：

``` powershell
miniserve xxx.iso
```

设置自定义index替代文件列表：

``` powershell
miniserve --index index.html
```

详细使用请参考[文档](https://github.com/svenstaro/miniserve#how-to-use)。

## [dufs](https://github.com/sigoden/dufs)

> Dufs is a distinctive utility file server that supports static serving, uploading, searching, accessing control, webdav...

这两个项目的功能几乎一样，界面有所区别，dufs也是`rust`编写的。

实例截图：

![](https://imgs.boringhex.top/blog/20230209153904.png)

### 特性

- 静态文件服务
- 将文件夹下载为zip文件
- 上传文件和文件夹（拖放）
- 搜索文件
- 部分响应（并行/恢复下载）
- 路径级访问控制
- 支持https
- 支持webdav
- 易于使用

### 安装

``` powershell
❯ scoop install dufs       
WARN  Scoop uses 'aria2c' for multi-connection downloads.
WARN  Should it cause issues, run 'scoop config aria2-enabled false' to disable it.
WARN  To disable this warning, run 'scoop config aria2-warning-enabled false'.
Installing 'dufs' (0.31.0) [64bit] from main bucket
Starting download with aria2 ...
Download: Download Results:
Download: gid   |stat|avg speed  |path/URI
Download: ======+====+===========+=======================================================
Download: 983615|OK  |   1.1MiB/s|C:/Users/yp.wang/scoop/cache/dufs#0.31.0#https_github.com_sigoden_dufs_releases_download_v0.31.0_dufs-v0.31.0-x86_64-pc-windows-msvc.zip
Download: Status Legend:
Download: (OK):download completed.
Checking hash of dufs-v0.31.0-x86_64-pc-windows-msvc.zip ... ok.
Extracting dufs-v0.31.0-x86_64-pc-windows-msvc.zip ... done.
Linking ~\scoop\apps\dufs\current => ~\scoop\apps\dufs\0.31.0
Creating shim for 'dufs'.
'dufs' (0.31.0) was installed successfully!
```

其它系统的安装请参考[文档](https://github.com/sigoden/dufs#install)

### 使用

``` powershell
❯ dufs -h
dufs 0.23.0
sigoden <sigoden@gmail.com>
Dufs is a distinctive utility file server - https://github.com/sigoden/dufs

USAGE:
    dufs.exe [OPTIONS] [--] [path]

ARGS:
    <path>    Specific path to serve [default: .]

OPTIONS:
    -b, --bind <addr>...         Specify bind address
    -p, --port <port>            Specify port to listen on [default: 5000]
        --path-prefix <path>     Specify an path prefix
        --hidden <value>         Hide directories from directory listings, separated by `,`
    -a, --auth <rule>...         Add auth for path
        --auth-method <value>    Select auth method [default: digest] [possible values: basic, digest]
    -A, --allow-all              Allow all operations
        --allow-upload           Allow upload files/folders
        --allow-delete           Allow delete files/folders
        --allow-search           Allow search files/folders
        --allow-symlink          Allow symlink to files/folders outside root directory
        --enable-cors            Enable CORS, sets `Access-Control-Allow-Origin: *`
        --render-index           Serve index.html when requesting a directory, returns 404 if not found index.html
        --render-try-index       Serve index.html when requesting a directory, returns directory listing if not found index.html
        --render-spa             Serve SPA(Single Page Application)
        --tls-cert <path>        Path to an SSL/TLS certificate to serve with HTTPS
        --tls-key <path>         Path to the SSL/TLS certificate's private key
    -h, --help                   Print help information
    -V, --version                Print version information
```

伺服当前目录：

``` powershell
dufs
```

允许 upload、delete、search等所有操作：

``` powershell
dufs -A
```

详细使用请参考[文档](https://github.com/sigoden/dufs#examples)。

