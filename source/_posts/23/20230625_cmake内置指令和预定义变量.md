---
title: cmake内置指令和预定义变量
date: 2023-06-25 11:58:42
categories:
tags:
    - esp32
    - cmake
    - ci/cd
---

## 内置指令

CMake 内置了很多指令，下面列举了一些常用的指令：

1. `add_executable`：指定生成可执行文件。
2. `add_library`：指定生成库文件。
3. `add_subdirectory`：添加子目录。
4. `aux_source_directory`：自动收集目录下的源文件。
5. `cmake_minimum_required`：指定所需的 CMake 最低版本。
6. `execute_process`：在构建时执行外部程序。
7. `find_package`：查找库文件和头文件。
8. `include_directories`：指定头文件搜索路径。
9. `link_directories`：指定库文件搜索路径。
10. `list`：操作列表，例如添加、删除、查找、排序等。
11. `message`：输出信息、警告、错误等消息。
12. `project`：声明项目名称和使用的编程语言。
13. `set`：设置变量的值。
14. `target_include_directories`：为目标指定头文件搜索路径。
15. `target_link_libraries`：为目标指定链接库。
16. `option`：提供一个用户可以选择的选项。
17. `file`：对文件进行操作，如读取、写入、删除等。
18. `install`：指定项目安装时的目标文件和路径。
19. `string`：操作字符串，例如转换为大写、小写、比较等。
20. `if`、`elseif`、`else`、`endif`：条件语句。

<!-- more -->

## 预定义变量

CMake 在处理不同平台和系统时提供了许多预定义变量，以下是一些常用的预定义变量：

1. `CMAKE_SYSTEM_NAME`：当前系统的名称，如 "Linux"，"Windows"，"Darwin"（macOS）或 "FreeBSD" 等。
2. `CMAKE_HOST_SYSTEM_NAME`：构建 CMake 时使用的系统名称，与 `CMAKE_SYSTEM_NAME` 类似，但适用于交叉编译。
3. `CMAKE_SYSTEM_PROCESSOR`：目标平台的处理器架构，如 "x86_64"，"i686"，"arm" 或 "aarch64"。
4. `CMAKE_HOST_SYSTEM_PROCESSOR`：CMake 构建时使用的系统的处理器架构。
5. `CMAKE_SYSTEM_VERSION`：系统的版本号或其他版本信息。
2. `CMAKE_HOST_SYSTEM_VERSION`：CMake 构建时使用的系统的版本信息。
3. `CMAKE_SYSTEM`：当前系统的名称和版本，为 `CMAKE_SYSTEM_NAME` 和 `CMAKE_SYSTEM_VERSION` 的组合。
4. `CMAKE_HOST_SYSTEM`：CMake 构建时使用的系统的名称和版本。
3. `CMAKE_CROSSCOMPILING`：如果当前构建是交叉编译，则此变量值为真。
4.  `CMAKE_FIND_ROOT_PATH`：在交叉编译时，用于寻找目标系统上支持库和头文件的路径。
1.  `CMAKE_SIZEOF_VOID_P`：CMake 编译器检测到的目标体系结构的 `void *` 的大小，可用于确定是 32 位还是 64 位系统。
1.  `UNIX`：如果当前系统是 Unix 类似系统（包括 Linux，macOS 和其他 Unix 系统），则此变量值为真。
2.  `APPLE`：如果当前系统是基于 Apple 的操作系统（如 macOS），则此变量值为真。
1.  `WIN32`：如果当前系统是基于 Windows 的操作系统，此变量值为真。
1.  `CYGWIN`：如果当前系统是 Cygwin（Windows 上运行的 Unix 类似环境），此变量值为真。
1.  `MINGW`：如果当前系统使用 MinGW 编译器（适用于 Windows），此变量值为真。
2.  `MSVC`：如果当前构建使用 Microsoft Visual C++ 编译器，则此变量值为真。
3.  `CMAKE_COMPILER_IS_GNUCXX`：如果当前构建使用 GNU C++ 编译器，则此变量值为真。

要获取关于所有 CMake 指令、变量和选项的更全面的了解，请参考 [CMake documentation](https://cmake.org/cmake/help/latest/)。
