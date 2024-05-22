---
title: czkawka-强大易用的重复文件查找和删除工具
date: 2023-03-22 10:11:32
categories:
tags:
  - rust
  - 工具
---

> Czkawka (tch•kav•ka (IPA: [ˈʧ̑kafka]), "hiccup" in Polish) is a simple, fast and free app to remove unnecessary files from your computer.
> Multi functional app to find duplicates, empty folders, similar images etc.

## [Czkawka](https://github.com/qarmin/czkawka)

### Features

- 使用内存安全的Rust语言编写
- 超快，得益于一些高级算法和多线程技术
- 免费，开源，无广告
- 多平台支持，Linux，Windows，macOS，FreeBSD，还会更多
- 缓存支持，二次扫描比首次更快
- 命令行接口，便于自动化
- gui使用gtk4，界面与fslint很像
- 没后门，不访问互联网，也不收集用户信息和统计信息
- 多语言
- 内置多种工具
  - 重复文件，基于文件名、大小或哈希
  - 空文件夹，查找指定目录中的空文件夹
  - 大文件，在指定位置查找大文件
  - 空文件
  - 临时文件
  - 相似图片
  - 相似视频
  - 相同音乐文件
  - 无效的符号链接
  - 损坏的文件
  - 不匹配的文件扩展名

<!-- more -->

<iframe src="//player.bilibili.com/player.html?bvid=BV1ax4y1A7vN&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

### 支持的操作系统

- Linux - Ubuntu 22.04+, Fedora 36+, Alpine Linux 3.16+, Debian 12+ and a lot of more
- Windows - 7, 8.1, 10, 11
- MacOS - 10.15+

如果需要gui使用gtk3的旧版本以支持更多操作系统，比如Ubuntu20.04，可以使用[4.1.0](https://github.com/qarmin/czkawka/releases/tag/4.1.0)或更早的版本。

### 安装

Windows用户可以使用scoop进行安装：

``` powershell
# 只安装命令行程序
scoop install czkawka

# 安装gui
scoop install czkawka-gui
```

其他操作系统上的安装方法请参考[文档](https://github.com/qarmin/czkawka/blob/master/instructions/Installation.md)。

### 如何使用

gui的使用非常简单，在页面指定搜索路径就可以，然后配置相应的工具和后续动作。

![](https://imgs.boringhex.top/blog/czkawka-gui.png)

#### 命令行的使用

``` powershell
❯ czkawka
czkawka 5.1.0

USAGE:
    czkawka.exe <COMMAND> [SCFLAGS] [SCOPTIONS]

OPTIONS:
  -h, --help     Print help
  -V, --version  Print version

SUBCOMMANDS:
  dup            Finds duplicate files
  empty-folders  Finds empty folders
  big            Finds big files
  empty-files    Finds empty files
  temp           Finds temporary files
  image          Finds similar images
  music          Finds same music by tags
  symlinks       Finds invalid symlinks
  broken         Finds broken files
  video          Finds similar video files
  ext            Finds files with invalid extensions
  tester         Small utility to test supported speed of
  help           Print this message or the help of the given subcommand(s)

    try "czkawka.exe <COMMAND> -h" to get more info about a specific tool

EXAMPLES:
    czkawka dup -d /home/rafal -e /home/rafal/Obrazy  -m 25 -x 7z rar IMAGE -s hash -f results.txt -D aeo
    czkawka empty-folders -d /home/rafal/rr /home/gateway -f results.txt
    czkawka big -d /home/rafal/ /home/piszczal -e /home/rafal/Roman -n 25 -x VIDEO -f results.txt
    czkawka empty-files -d /home/rafal /home/szczekacz -e /home/rafal/Pulpit -R -f results.txt
    czkawka temp -d /home/rafal/ -E */.git */tmp* *Pulpit -f results.txt -D
    czkawka image -d /home/rafal -e /home/rafal/Pulpit -f results.txt
    czkawka music -d /home/rafal -e /home/rafal/Pulpit -z "artist,year, ARTISTALBUM, ALBUM___tiTlE"  -f results.txt
    czkawka symlinks -d /home/kicikici/ /home/szczek -e /home/kicikici/jestempsem -x jpg -f results.txt
    czkawka broken -d /home/mikrut/ -e /home/mikrut/trakt -f results.txt
    czkawka extnp -d /home/mikrut/ -e /home/mikrut/trakt -f results.txt

❯ czkawka dup -h
Finds duplicate files

Usage: czkawka.exe dup [OPTIONS] --directories <DIRECTORIES>

Options:
  -d, --directories <DIRECTORIES>
          Directorie(s) to search
  -e, --excluded-directories <EXCLUDED_DIRECTORIES>
          Excluded directorie(s)
  -E, --excluded-items <EXCLUDED_ITEMS>
          Excluded item(s)
  -m, --minimal-file-size <MINIMAL_FILE_SIZE>
          Minimum size in bytes [default: 8192]
  -i, --maximal-file-size <MAXIMAL_FILE_SIZE>
          Maximum size in bytes [default: 18446744073709551615]
  -c, --minimal-cached-file-size <MINIMAL_CACHED_FILE_SIZE>
          Minimum cached file size in bytes [default: 257144]
  -x, --allowed-extensions <ALLOWED_EXTENSIONS>
          Allowed file extension(s)
  -s, --search-method <SEARCH_METHOD>
          Search method (NAME, SIZE, HASH) [default: HASH]
  -D, --delete-method <DELETE_METHOD>
          Delete method (AEN, AEO, ON, OO, HARD) [default: NONE]
  -t, --hash-type <HASH_TYPE>
          Hash type (BLAKE3, CRC32, XXH3) [default: BLAKE3]
  -f, --file-to-save <file-name>
          Saves the results into the file
  -R, --not-recursive
          Prevents from recursive check of folders
  -l, --case-sensitive-name-comparison
          Use case sensitive name comparison
  -L, --allow-hard-links
          Do not ignore hard links
      --dryrun
          Do nothing and print the operation that would happen.
  -h, --help
          Print help (see more with '--help')

EXAMPLE:
    czkawka dup -d /home/rafal -e /home/rafal/Obrazy  -m 25 -x 7z rar IMAGE -s hash -f results.txt -D aeo
```

来个实例：

``` powershell
# 创建并进入测试目录
❯ mkdir test && cd test

    Directory: C:\Users\yp.wang

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----           2023/3/22    12:02                test

# 创建两个内容为"中文"的txt文件
❯ echo 中文 > test1.txt
❯ echo 中文 > test2.txt

# 查找重复文件
❯ czkawka dup -d C:\Users\yp.wang\test\ -m 0
error: invalid value '0' for '--minimal-file-size <MINIMAL_FILE_SIZE>': Minimum file size must be at least 1 byte

For more information, try '--help'.

~\test
❯ czkawka dup -d C:\Users\yp.wang\test\ -m 1
Set thread number to 12
Found 2 duplicated files in 1 groups with same content which took 8 B:
Size - 8 B (8) - 2 files
C:\users\yp.wang\test\test1.txt
C:\users\yp.wang\test\test2.txt
----

-------------------------------MESSAGES--------------------------------
Loaded from cache ⁨0⁩ entries
Saved to file ⁨2⁩ cache entries
Loaded from cache ⁨0⁩ entries
Saved to file ⁨0⁩ cache entries
---------------------------END OF MESSAGES-----------------------------
```

czkawka目前还不支持相对路径，必须使用绝对路径。另外查找重复文件最小要大于0，空文件有另外的命令。

其他命令的用法可以参考[文档](https://github.com/qarmin/czkawka/blob/master/instructions/Instruction.md)，还有[tips&tricks](https://github.com/qarmin/czkawka/blob/master/instructions/Instruction.md#tips-tricks-and-known-bugs)。

### 性能测试

因为czkawka是用rust编写，更快是设计目标之一，期望替代python编写的FSlint和DupeGuru，以下做了这几个工具的对比测试。

测试主机：256 GB SSD，i7-4770 CPU.

作者准备了一个磁盘并执行了测试，没有任何文件夹异常，并且禁用了对硬链接的忽略。该磁盘包含 363 215 个文件，占用 221.8 GB，并在 62093 组中有 31790 个重复文件，占用 4.1 GB。

在所有程序中都将文件最小大小设为1KB。

| App                         | Executing Time |
|:----------------------------|:--------------:|
| FSlint 2.4.7 (First Run)    |      86s       |
| FSlint 2.4.7 (Second Run)   |      43s       |
| Czkawka 3.0.0 (First Run)   |       8s       |
| Czkawka 3.0.0 (Second Run)  |       7s       |
| DupeGuru 4.1.1 (First Run)  |      22s       |
| DupeGuru 4.1.1 (Second Run) |      21s       |

又使用Mprof检查了FSlint和DupeGuru的内存使用，用Heaptrack检查czkawka。

| App             | Idle Ram | Max Operational Ram Usage | Stabilized after search |
|:----------------|:--------:|:-------------------------:|:-----------------------:|
| FSlint 2.4.7    |  62 MB   |          164 MB           |         158 MB          |
| Dupeguru 4.1.1  |  90 MB   |          170 MB           |         166 MB          |
| Czkawka 3.0.0   |  12 MB   |          122 MB           |          60 MB          |


在Dupeguru中，启用了检查不同尺寸的图像以匹配Czkawka行为。 这两个应用程序都使用缓存机制，因此第二次扫描非常快。

检查占用 6.6GB 的 10949 个文件的类似图像：

| App                         | Scan time |
|:----------------------------|:---------:|
| Czkawka 3.0.0 (First Run)   |   276s    |
| Czkawka 3.0.0 (Second Run)  |    1s     |
| DupeGuru 4.1.1 (First Run)  |   539s    |
| DupeGuru 4.1.1 (Second Run) |    1s     |

检查占用 1.7GB 的 349 个文件的类似图像：

| App                         | Scan time  |
|:----------------------------|:----------:|
| Czkawka 3.0.0 (First Run)   |    54s     |
| Czkawka 3.0.0 (Second Run)  |     1s     |
| DupeGuru 4.1.1 (First Run)  |    55s     |
| DupeGuru 4.1.1 (Second Run) |     1s     |

### 与其他工具的对比

Bleachbit是查找和删除临时文件的大师，而Czkawka只找到最基本的文件。因此，不应直接比较这两个应用程序或将其视为彼此的替代品。

|                          |   Czkawka   |   FSlint   |     DupeGuru      |  Bleachbit  |
|:------------------------:|:-----------:|:----------:|:-----------------:|:-----------:|
|         Language         |    Rust     |   Python   |   Python/Obj-C    |   Python    |
|            OS            | Lin,Mac,Win |    Lin     |    Lin,Mac,Win    | Lin,Mac,Win |
|        Framework         |    GTK 4    |   PyGTK2   | Qt 5 (PyQt)/Cocoa |   PyGTK3    |
|     Duplicate finder     |     ✔       |      ✔     |        ✔          |             |
|       Empty files        |     ✔       |      ✔     |                   |             |
|      Empty folders       |     ✔       |      ✔     |                   |             |
|     Temporary files      |     ✔       |      ✔     |                   |     ✔       |
|        Big files         |     ✔       |            |                   |             |
|      Similar images      |     ✔       |            |        ✔          |             |
|      Similar videos      |     ✔       |            |                   |             |
|  Music duplicates(tags)  |     ✔       |            |        ✔          |             |
|     Invalid symlinks     |     ✔       |      ✔     |                   |             |
|       Broken files       |     ✔       |            |                   |             |
|      Names conflict      |     ✔       |      ✔     |                   |             |
| Invalid names/extensions |     ✔       |      ✔     |                   |             |
|    Installed packages    |             |      ✔     |                   |             |
|          Bad ID          |             |      ✔     |                   |             |
|  Non stripped binaries   |             |      ✔     |                   |             |
|   Redundant whitespace   |             |      ✔     |                   |             |
|    Overwriting files     |             |      ✔     |                   |     ✔       |
|    Multiple languages    |     ✔       |      ✔     |        ✔          |     ✔       |
|      Cache support       |     ✔       |            |        ✔          |             |
|  In active development   |     Yes     |      No    |        Yes        |     Yes     |

### Other apps

互联网上有许多与Czkawka类似的应用程序，各有长短：

#### GUI

- [DupeGuru](https://github.com/arsenetar/dupeguru) - 许多自定义选项，很棒的照片比较工具
- [FSlint](https://github.com/pixelb/fslint) - 有点过时，但仍然有一些工具在 Czkawka 中不可用
- [AntiDupl.NET](https://github.com/ermig1979/AntiDupl) - 显示比较图像的大量元数据
- [Video Duplicate Finder](https://github.com/0x90d/videoduplicatefinder) - 查找相似的视频（令人惊讶，不是吗），支持视频缩略图


#### CLI

如果正在寻找真正优秀且功能丰富的控制台应用程序，可以对比下面几个工具：

- [Fclones](https://github.com/pkolaczk/fclones) - 查找重复项的最快工具之一，同样使用Rust编写
- [Rmlint](https://github.com/sahib/rmlint) - 不错的控制台界面，功能也丰富
- [RdFind](https://github.com/pauldreik/rdfind) - 快速，但用C++编写 ¯\\\_(ツ)\_/¯
