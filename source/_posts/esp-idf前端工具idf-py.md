---
title: esp-idf前端工具idf.py
date: 2023-07-04 10:12:37
categories:
tags:
---
在之前的文章 [ESP32 VS Code 开发环境"hello world"](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481620&idx=1&sn=6d148cc74ea5bf67b54f4d4a959eb1ed&chksm=84ad70d7b3daf9c14c37c992a1ea1e0c545af42524c3f856def65d48e1b00fca82948703e4db#rd) 中，我们在vs code中完成了esp32 "hello world"程序的开发。乐鑫将很多操作集成到了vs code插件中，在gui中以按钮的形式与用户进行交互。而在这些按钮的背后，则是`idf.py`。

## `idf.py`

`idf.py`是一个用python编写的命令行工具，在ESP-IDF中，它提供了一个前端界面，管理工程构建、工程部署及工程调试等操作，我们之前提到的CMake、Ninja等工具也是由它来整合在一起的：

- `CMake` 用于配置要构建的工程
- `Ninja` 用于构建工程
- `esptool.py` 用于烧录目标芯片

注意：`idf.py`应在ESP-IDF工程目录下运行，即包含顶层`CMakeLists.txt`文件的目录。

<!-- more -->

### 常用命令

#### 创建新工程

```powershell
idf.py create-project <project name>
```

此命令将创建一个新的 ESP-IDF 工程。此外，使用 `--path` 选项可指定工程创建路径。

#### 创建新组件

```powershell
idf.py create-component <component name>
```

此命令将创建一个新的组件，包含构建所需的最基本文件集。使用 `-C` 选项可指定组件创建目录。

#### 选择目标芯片

ESP-IDF 支持多个目标芯片，运行 `idf.py --list-targets` 查看当前 ESP-IDF 版本支持的所有目标芯片。

```powershell
idf.py set-target <target>
```

此命令将设置当前工程的目标芯片。

注意：`idf.py set-target` 将清除构建目录，并重新生成 `sdkconfig` 文件，原来的 `sdkconfig` 文件保存为 `sdkconfig.old`。

`idf.py set-target` 命令与以下操作效果相同：

1. 清除构建目录 (`idf.py fullclean`)
2. 删除 `sdkconfig` 文件 (`mv sdkconfig sdkconfig.old`)
3. 使用新的目标芯片重新配置工程 (`idf.py -DIDF_TARGET=esp32 reconfigure`)

所需的 `IDF_TARGET` 还可以作为环境变量（如 `export IDF_TARGET=esp32s2`）或 CMake 变量（如将 `-DIDF_TARGET=esp32s2` 作为 CMake 或 idf.py 的参数）传递。在经常使用同类芯片的情况下，设置环境变量将使操作更加便利。

要给指定工程设定 `IDF_TARGET` 的默认值，请将 `CONFIG_IDF_TARGET` 选项添加到该工程的 `sdkconfig.defaults` 文件（如 `CONFIG_IDF_TARGET="esp32s2"`）。若未通过使用环境变量、CMake 变量或 idf.py set-target 命令等方法指定 `IDF_TARGET`，则默认使用该选项的值。

若未通过以上任一方法设置目标芯片，构建系统将默认使用 `esp32`。

#### 启动图形配置工具

```powershell
idf.py menuconfig
```

#### 构建工程

```powershell
idf.py build
```

此命令将构建当前目录下的工程，具体步骤如下：

1. 若有需要，创建构建子目录 `build` 保存构建输出文件，使用 `-B` 选项可改变子目录的路径。
2. 必要时运行 CMake 配置工程，并为主要构建工具生成构建文件。
3. 运行主要构建工具（Ninja 或 GNU Make）。默认情况下，构建工具会完成自动检测，也可通过将 -G 选项传递给 idf.py 来显式设置构建工具。

构建是增量行为，因此若上次构建结束后，源文件或配置并未发生更改，则不会执行任何操作。

此外，使用 `app`、`bootloader` 或 `partition-table` 参数运行此命令，可选择仅构建应用程序、引导加载程序或分区表。

#### 清除构建输出

```powershell
idf.py clean
```

此命令可清除构建目录中的构建输出文件，下次构建时，工程将完全重新构建。注意，使用此选项不会删除构建文件夹内的 CMake 配置输出。

#### 删除所有构建内容

```powershell
idf.py fullclean
```

此命令将删除所有 build 子目录内容，包括 CMake 配置输出。下次构建时， CMake 将重新配置其输出。注意，此命令将递归删除构建目录下的 **所有** 文件（工程配置将保留），请谨慎使用。

#### 烧录工程

```powershell
idf.py flash
```

此命令将在需要时自动构建工程，随后将其烧录到目标芯片。使用 `-p` 和 `-b` 选项可分别设置串口名称和烧录程序的波特率。

环境变量 `ESPPORT` 和 `ESPBAUD` 可分别设置 `-p` 和 `-b` 选项的默认值，在命令行上设置这些选项的参数可覆盖默认值。

与 `build` 命令类似，使用 `app`、`bootloader` 或 `partition-table` 参数运行此命令，可选择仅烧录应用程序、引导加载程序或分区表。
