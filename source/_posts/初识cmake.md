---
title: 初识cmake
date: 2023-06-25 11:46:09
categories:
tags:
    - esp32
    - cmake
    - ci/cd
---

ESP-IDF工具链依赖cmake和ninja来管理项目构建过程，今天先学习cmake。

## [CMake](https://cmake.org/)

CMake是一个开源的跨平台自动化构建系统，用于管理软件编译和构建过程。它可以生成各种平台的Makefile或者项目文件，使得软件的编译和构建过程可以在不同的平台和编译器上进行。CMake使用CMakeLists.txt文件来描述项目的构建设置和依赖关系。

CMake可以帮助开发人员轻松管理和构建大型项目。CMake的优势主要体现在以下几个方面：

1. 跨平台：CMake支持多种平台，如Windows、Linux、macOS等。这意味着您可以使用相同的CMakeLists.txt文件在不同的平台上构建项目，而无需为每个平台编写特定的构建脚本。

2. 自动生成构建文件：CMake可以根据项目配置生成各种构建系统的文件，如Makefile、Visual Studio解决方案等。这使得开发人员可以专注于编写代码，而不必花时间维护构建脚本。

3. 易于维护：CMake使用简洁的语法，易于理解和维护。此外，CMake的模块化设计使得将项目分解为多个独立的组件变得更加容易，这有助于提高项目的可维护性。

4. 强大的功能：CMake提供了丰富的内置函数和模块，支持各种编译器和构建工具。此外，CMake还支持自定义函数和模块，以满足特定项目的需求。

5. 高效的构建：CMake支持并行构建，可以有效地利用多核处理器来加速编译过程。此外，CMake还支持增量构建，这意味着当您对项目进行修改时，只有受影响的部分需要重新构建，从而节省了大量时间。

6. 社区支持：CMake有一个活跃的社区，开发人员可以在其中寻求帮助和分享经验。此外，许多开源项目已经采用CMake作为其构建系统，这意味着您可以在很多实际项目中找到CMake的使用示例。

<!-- more -->

CMake是一个强大且易于使用的构建工具，可以帮助开发者更轻松地管理和构建跨平台的软件项目。

## 安装和配置

1. 安装CMake：

   a. Windows：
      - 访问CMake官方网站下载页：https://cmake.org/download/
      - 选择适合您的操作系统的CMake安装程序（例如，Windows win64-x64 Installer）
      - 下载并运行安装程序，按照提示进行安装

   b. Linux：
      - 在终端中运行以下命令：
        ```
        sudo apt-get update
        sudo apt-get install cmake
        ```

   c. macOS：
      - 使用Homebrew安装CMake：
        ```
        brew install cmake
        ```
      - 如果您没有安装Homebrew，请访问https://brew.sh/ 以获取安装说明。

2. 配置CMake：

   a. Windows：
      - 在安装过程中，选择“将CMake添加到系统PATH中”，以便在命令行中使用CMake。
      - 您可以通过运行`cmake --version`来验证CMake是否已成功安装。
 
   b. Linux和macOS：
      - 在大多数情况下，CMake会自动添加到系统PATH中。
      - 您可以通过运行`cmake --version`来验证CMake是否已成功安装。

3. 使用CMake：

   - 在项目根目录中创建一个名为`CMakeLists.txt`的文件。在此文件中，您可以使用CMake语法定义项目设置和依赖项。
   - 在终端中，导航到项目根目录，然后运行以下命令生成构建文件：
     ```
     mkdir build
     cd build
     cmake ..
     ```
   - 运行`cmake --build .`命令以构建项目。

更多关于CMake的使用方法和语法，请查看[CMake官方文档](https://cmake.org/cmake/help/latest/)。

## Windows Hello World

下面将以一个简单的Hello World程序为例，讲解CMake最基本的用法：

1. 创建源代码文件

新建一个文件夹，例如叫"hello_world"，然后在这个文件夹中创建一个名为main.cpp的文件，内容如下：

```cpp
#include <iostream>

int main() {
    std::cout << "Hello World!" << std::endl;
    return 0;
}
```

2. 创建CMakeLists.txt文件

在"hello_world"文件夹中创建一个名为CMakeLists.txt的文件，内容如下：

```cmake
cmake_minimum_required(VERSION 3.0)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world main.cpp)
```

这个CMakeLists.txt文件的内容表示：

- 设置所需的CMake最低版本为3.0
- 定义一个名为"hello_world"的项目
- 设置C++11标准
- 添加一个可执行文件"hello_world"，源文件为"main.cpp"

3. 使用CMake生成构建文件

打开命令提示符或PowerShell，进入"hello_world"文件夹，然后执行以下命令：

```powershell
mkdir build
cd build
cmake ..
```

这将在"build"文件夹中生成构建文件，例如Makefile或者Visual Studio解决方案文件。

4. 编译和运行程序

在"build"文件夹中执行以下命令来编译程序：

```powershell
cmake --build .
```

然后在"build"文件夹中找到编译好的可执行文件"hello_world.exe"，双击运行或者在命令行中输入：

```powershell
# vs2022解决方案路径
.\Debug\hello_world.exe
```

这将会输出"Hello World!"。

通过上述步骤，可以看到CMake的基本用法，它可以帮助我们在不同的平台和编译器上自动生成构建文件，简化编译过程。

完整示例过程：

```powershell
❯ mkdir build && cd build

    Directory: C:\Users\yp.wang\Desktop\esp-test\hello

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----           2023/6/20    11:30                build


esp-test\hello\build
❯ cmake ..
-- Building for: Visual Studio 17 2022
-- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19045.
-- The C compiler identification is MSVC 19.33.31629.0
-- The CXX compiler identification is MSVC 19.33.31629.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.33.31629/bin/Hostx64/x64/cl.exe - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.33.31629/bin/Hostx64/x64/cl.exe - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: C:/Users/yp.wang/Desktop/esp-test/hello/build

esp-test\hello\build via △ v3.24.3 took 5s 
❯ cmake --build .
MSBuild version 17.3.1+2badb37d1 for .NET Framework
  Checking Build System
  Building Custom Rule C:/Users/yp.wang/Desktop/esp-test/hello/CMakeLists.txt
  main.cpp
  hello_world.vcxproj -> C:\Users\yp.wang\Desktop\esp-test\hello\build\Debug\hello_world.exe
  Building Custom Rule C:/Users/yp.wang/Desktop/esp-test/hello/CMakeLists.txt

esp-test\hello\build via △ v3.24.3 took 2s 
❯ .\Debug\hello_world.exe
Hello World!
```
