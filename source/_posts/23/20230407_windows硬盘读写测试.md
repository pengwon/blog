---
title: windows硬盘读写测试
date: 2023-02-19 15:01:18
categories:
tags:
  - windows
  - powershell
---

说到硬盘读写测试，多数文章会建议使用[CrystalDiskMark](https://crystalmark.info/en/software/crystaldiskmark/)这个软件，GUI操作，简单方便。

其实Windows有自带命令可以评估硬件，其中就包括硬盘测试：

<!-- more -->

``` powershell
❯ winsat -?

Windows 系统评估工具


命令行用法:
    WINSAT <assessment_name> [switches]

需要提供评估名称。与之相反，开关可选。
有效的评估名称已显示在 Vista 中，包括:

    formal              运行完整的评估

    dwm         运行桌面窗口管理器评估
            - 重新评估系统图形功能并
              重新启动桌面窗口管理器。

    cpu         运行 CPU 评估。
    mem         运行系统内存评估。
    d3d         运行 d3d 评估
                (请注意，d3d 评估不再运行工作负荷。
                为了向后兼容，将报告预定的分数和度量结果。)
    disk                运行存储评估
    media               运行媒体评估
    mfmedia             运行基于媒体基础的评估
    features    只运行功能评估
            -枚举系统的功能。
            - 它最好与 -xml <filename> 开关使用
            以保存数据。
            -“eef”开关可用来枚举其他
            功能，如光盘、      内存模块
            和其他项。

PRE-POPULATION:
用于预填充 WinSAT 评估结果的新命令行选项是:

    Winsat prepop [-datastore <directory>] [ -graphics | -cpu | -mem | -disk | -dwm ]


这会生成文件名包含“prepop”的 WinSAT xml 文件。例如:
    0008-09-26 14.48.28.542 Cpu.Assessment (Prepop).WinSAT.xml

文件名模式为:
    %IdentifierDerivedFromDate% %Component%.Assessment(Prepop).WinSAT.xml

数据存储目录选项可为生成的 xml 文件指定其他目标位置。
如果没有指定位置，会将全部内容预填充到
    %WINDIR%\performance\winsat\datastore。

若要生成完整的结果 xml 文件，请使用“winsat prepop”。

还可以为子系统预填充结果，如 CPU，
受以下依存关系制约:

    CPU 评估存在对内存评估的次要依存关系
    内存评估存在对 CPU 评估的次要依存关系
    图形评估存在对 CPU 评估和内存评估的次要依存关系
    DWM 评估可以独立运行
    磁盘评估可以独立运行

如果次要依存关系的评估不存在，WinSAT 将与
请求的主评估一起运行次要评估。

例如，“winsat prepop -cpu”将运行 CPU 和内存测试，
前提是用于内存测试的 xml 文件不存在。



其他新的 Win7 评估选项:

    dwmformal   运行桌面窗口管理器评估可生成 WinSAT 图形分数
    cpuformal   运行 CPU 评估可生成 WinSAT 处理器分数
    memformal   运行内存评估可生成 WinSAT 内存(RAM)分数
    graphicsformal      运行图形评估可生成 WinSAT 游戏图形分数
    diskformal  运行磁盘评估可生成 WinSAT 主硬盘分数

所有正式评估将数据(xml 文件)保存在
        %WINDIR%\performance\winsat\datastore 中。

如果已经预填充系统(使用通过“winsat prepop”选项生成的文件)，
则不需要运行正式评估。


SUB-ASSESSMENTS:
研究结果时，可以方便地查看单个评估。
用于运行游戏图形子评估的选项包括:

    Winsat graphicsformal3d
    Winsat graphicsformalmedia

    DX9变体:
        Winsat d3d -dx9
        winsat d3d -batch
        winsat d3d -alpha
        winsat d3d -tex
        winsat d3d -alu

    DWM/DX10变体:
        Winsat d3d -dx10
        winsat d3d -dx10 -alpha
        winsat d3d -dx10 -tex
        winsat d3d -dx10 -alu
        winsat d3d -dx10 -batch
        winsat d3d -dx10 -geomf4
        winsat d3d -dx10 -geomf27
        winsat d3d -dx10 -geomv8
        winsat d3d -dx10 -gemov32
        winsat d3d -dx10 -cbuffer



用于后续的在同一计算机上运行的正式评估的选项:

当存在完整的 winsat 正式文件，并且请求另一个
“winsat formal”运行时，“winsat formal”的默认行为是
    1) 如果组件更改表示需要重新运行评估，则递增运行，
        例如，如果视频卡已更新
    2) 如果没有检测到组件更新，请重新运行所有评估。

    重新启动选项可启用除默认行为之外的行为。语法如下:
        Winsat formal -restart [clean|never]

        Winsat formal -restart          返回所有评估。
        Winsat formal -restart never    试图递增地运行。
        Winsat formal -restart clean    重新运行所有评估，并提供与“forgethistory”相同的功能。
        Winsat forgethistory            选择 forgethistory 就好像第一次对计算机分级。


其他命令行选项:
    -v                  启用详细输出
    -xml                        将 XML 输出保存到“filename”

    <command> -log <fn> 生成一个与指定命令相关的日志文件，如磁盘
                -log开关可以与任何 WinSAT 命令一起使用。

    viewlog -i <filename>       转储日志文件的结果。
    viewevents          用于查看事件日志中的相关 winsat 事件。
                (这可启动事件日志)
    query                       可用于查询当前的数据存储。
```

> 注意这个命令需要管理员权限。

测试下硬盘：

``` powershell
❯ winsat disk
Windows 系统评估工具
> 正在运行: 功能枚举 ''
> 运行时间 00:00:00.00
> 正在运行: 存储评估 '-ran -read -n 2'
> 运行时间 00:00:00.28
> 正在运行: 存储评估 '-seq -read -n 2'
> 运行时间 00:00:03.66
> 正在运行: 存储评估 '-seq -write -drive C:'
> 运行时间 00:00:02.11
> 正在运行: 存储评估 '-flush -drive C: -seq'
> 运行时间 00:00:00.66
> 正在运行: 存储评估 '-flush -drive C: -ran'
> 运行时间 00:00:00.63
> Dshow 视频编码时间                                 0.00000 s
> Dshow 视频解码时间                                 0.00000 s
> 媒体基础解码时间                                     0.00000 s
> Disk  Random 16.0 Read                       494.76 MB/s          8.3
> Disk  Sequential 64.0 Read                   2794.64 MB/s          9.2
> Disk  Sequential 64.0 Write                  1989.70 MB/s          9.0
> 顺序写操作的平均读取时间                                 0.080 ms          8.8
> 延迟: 95%                                      0.141 ms          8.9
> 延迟: 最大                                       8.492 ms          8.2
> 随机写操作的平均读取时间                                 0.072 ms          8.9
> 总运行时间 00:00:07.50
```

默认对系统盘进行测试，如果需要测试其它磁盘，使用`-drive`参数指定，比如：

``` powershell
winsat disk -drive d
```

如果需要输出详细信息，使用`-v`选项：

``` powershell
❯ winsat disk -v
Windows 系统评估工具
> 命令行 '"C:\WINDOWS\system32\WinSAT.exe" disk -v'
> DWM running... leaving it on
> 正在运行: 功能枚举 ''
> Gathering System Information
> Operating System                        : 10.0 Build-19045
> Processor                               : Intel(R) Xeon(R) CPU E5-2690 v2 @ 3.00GHz
> TSC Frequency                           : 0
> Number of Processors                    : 1
> Number of Cores                         : 10
> Number of CPUs                          : 20
> Number of Cores per Processor           : 10
> Number of CPUs Per Core                 : 2
> Cores have logical CPUs                 : YES
> L1 Cache and line Size                  : 32768  64
> L2 Cache and line Size                  : 262144  64
> L3 Cache and line Size                  : 26214400  64
> Total physical mem available to the OS  : 63.9 GB (68,643,934,208 bytes)
> Adapter Description                     : NVIDIA Quadro K6000
> Adapter Manufacturer                    : NVIDIA
> Adapter Driver Provider                 : NVIDIA
> Adapter Driver Version                  : 30.0.14.7298
> Adapter Driver Date (yy/mm/dd)          : 2022\1\5
> Has DX9 or better                       : Yes
> Has Pixel shader 2.0 or better          : Yes
> Has LDDM Driver                         : Yes
> Dedicated (local) video memory          : 12230.5MB
> System memory dedicated as video memory : 0MB
> System memory shared as video memory    : 32732MB
> Primary Monitor Size                    : 1920 X 1080  (2073600 total pixels)
> WinSAT is Official                       : Yes
> 运行时间 00:00:00.00
> 正在运行: 存储评估 '-ran -read -n 2'
模式标记 = 0x01000002
磁盘号 = 2
迭代 = 1
IO 计数 = 1000
顺序 IO 大小 = 65536
随机 IO 大小 = 16384
运行[1] 类型[0x01000002] 区域[0] - 441.989732 MB/秒
> 运行时间 00:00:00.30
> 正在运行: 存储评估 '-seq -read -n 2'
模式标记 = 0x01080001
磁盘号 = 2
迭代 = 1
IO 计数 = 256
顺序 IO 大小 = 65536
随机 IO 大小 = 16384
运行[1] 类型[0x01080001] 区域[0] - 1865.171421 MB/秒
运行[1] 类型[0x01080001] 区域[1] - 2790.908615 MB/秒
运行[1] 类型[0x01080001] 区域[2] - 2855.205396 MB/秒
运行[1] 类型[0x01080001] 区域[3] - 2655.116908 MB/秒
运行[1] 类型[0x01080001] 区域[4] - 1883.593897 MB/秒
运行[1] 类型[0x01080001] 区域[5] - 2889.233992 MB/秒
运行[1] 类型[0x01080001] 区域[6] - 2816.108139 MB/秒
运行[1] 类型[0x01080001] 区域[7] - 2842.574662 MB/秒
运行[1] 类型[0x01080001] 区域[8] - 2868.668758 MB/秒
运行[1] 类型[0x01080001] 区域[9] - 2846.266055 MB/秒
运行[1] 类型[0x01080001] 区域[10] - 2397.686233 MB/秒
运行[1] 类型[0x01080001] 区域[11] - 2874.079396 MB/秒
运行[1] 类型[0x01080001] 区域[12] - 2856.377756 MB/秒
运行[1] 类型[0x01080001] 区域[13] - 2849.967047 MB/秒
运行[1] 类型[0x01080001] 区域[14] - 2930.134603 MB/秒
运行[1] 类型[0x01080001] 区域[15] - 1797.348910 MB/秒
> 运行时间 00:00:03.61
> 正在运行: 存储评估 '-seq -write -drive C:'
模式标记 = 0x02000001
磁盘号 = 2
迭代 = 8
IO 计数 = 1000
顺序 IO 大小 = 65536
随机 IO 大小 = 16384

        请求大小为 65536000 的文件(物理偏移 0x27500200)。

运行[1] 类型[0x02000001] 区域[0] - 1426.094099 MB/秒
运行[2] 类型[0x02000001] 区域[0] - 1475.274401 MB/秒
运行[3] 类型[0x02000001] 区域[0] - 1467.329039 MB/秒
运行[4] 类型[0x02000001] 区域[0] - 1463.885363 MB/秒
运行[5] 类型[0x02000001] 区域[0] - 1469.523263 MB/秒
运行[6] 类型[0x02000001] 区域[0] - 1472.330782 MB/秒
运行[7] 类型[0x02000001] 区域[0] - 1307.835083 MB/秒
运行[8] 类型[0x02000001] 区域[0] - 794.552800 MB/秒
> 运行时间 00:00:02.08
> 正在运行: 存储评估 '-flush -drive C: -seq'
模式标记 = 0x04000001
磁盘号 = 2
迭代 = 1
IO 计数 = 2501
顺序 IO 大小 = 65536
随机 IO 大小 = 16384

        请求大小为 67108864 的文件(物理偏移 0x6b1a6a7300)。

Expected number of IOs: 2501
Number of IOs in trace file:2536
> 运行时间 00:00:00.66
> 正在运行: 存储评估 '-flush -drive C: -ran'
模式标记 = 0x04000002
磁盘号 = 2
迭代 = 1
IO 计数 = 2501
顺序 IO 大小 = 65536
随机 IO 大小 = 16384

        请求大小为 67108864 的文件(物理偏移 0x6b1a6a7300)。

Expected number of IOs: 2501
Number of IOs in trace file:2522
> 运行时间 00:00:00.64
> Dshow 视频编码时间                                 0.00000 s
> Dshow 视频解码时间                                 0.00000 s
> 媒体基础解码时间                                     0.00000 s
> Disk  Random 16.0 Read                       478.71 MB/s          8.3
> Disk  Sequential 64.0 Read                   2979.00 MB/s          9.2
> Disk  Sequential 64.0 Write                  1918.92 MB/s          9.0
> 顺序写操作的平均读取时间                                 0.073 ms          8.9
> 延迟: 95%                                      0.141 ms          8.9
> 延迟: 最大                                       12.391 ms          7.9
> 随机写操作的平均读取时间                                 0.072 ms          8.9
> 总运行时间 00:00:07.47
```

顺带测试下cpu和内存：

``` powershell
❯ winsat cpu  -encryption
Windows 系统评估工具
> 正在运行: 功能枚举 ''
> 运行时间 00:00:00.00
> 正在运行: CPU 评估 '-encryption'
> 运行时间 00:00:10.14
> CPU AES256 加密                                6761.72 MB/s
> Dshow 视频编码时间                                 0.00000 s
> Dshow 视频解码时间                                 0.00000 s
> 媒体基础解码时间                                     0.00000 s
> 总运行时间 00:00:10.86

❯ winsat mem
Windows 系统评估工具
> 正在运行: 功能枚举 ''
> 运行时间 00:00:00.00
> 正在运行: 系统内存性能评估 ''
> 运行时间 00:00:05.99
> 内存性能                                         43091.63 MB/s
> Dshow 视频编码时间                                 0.00000 s
> Dshow 视频解码时间                                 0.00000 s
> 媒体基础解码时间                                     0.00000 s
> 总运行时间 00:00:06.75
```
