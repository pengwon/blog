---
title: 详解CMakeLists.txt
date: 2023-06-25 11:51:53
categories:
tags:
    - esp32
    - cmake
    - ci/cd
---

在前一篇文章 [esp32 初识 cmake 构建工具](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481640&idx=1&sn=99a7765a84b2ef3ac5bf33fd4a3da05d&chksm=84ad70ebb3daf9fdb2ba56f473cbe85eebd0ce5bdd0de43495df9bbcf927f526a5f042647b8d#rd) 中，已经用"hellow world"程序介绍了cmake最基本的用法，今天详细讲解`CMakeLists.txt`文件。

## CMakeLists.txt

CMakeLists.txt文件是CMake构建工具的核心配置文件。它包含了构建项目所需的一系列指令和参数，CMake会根据这些信息生成相应的构建文件。CMakeLists.txt文件通常位于项目的顶层目录，并可在子目录中包含其他的CMakeLists.txt文件以组织复杂项目的构建过程。

CMakeLists.txt文件包含以下几类指令：

1. 项目设置：定义项目名称、版本、目标平台等信息
2. 指定源文件和头文件：指定项目中要编译的源文件和头文件
3. 添加子目录：将子目录加入构建过程，子目录中可以包含其它的CMakeLists.txt文件
4. 设置编译器和链接器选项：例如，指定编译标志、链接库等
5. 添加可执行文件和库：指定要生成的可执行文件、静态库或动态库
6. 添加依赖和链接库：指定项目需要的外部库和头文件路径

<!-- more -->

以下是一个简单的C语言项目的CMakeLists.txt文件示例：

```cmake
# 设置CMake的最低版本要求
cmake_minimum_required(VERSION 3.10)

# 设置项目名称和版本
project(MyProject VERSION 1.0)

# 设置C标准
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED True)

# 添加子目录，子目录下应包含自己的CMakeLists.txt文件
add_subdirectory(src)

# 指定源文件和头文件
set(SOURCES main.c)
set(HEADERS include/myheader.h)

# 添加可执行文件
add_executable(MyProject ${SOURCES} ${HEADERS})

# 添加库
add_library(MyLibrary STATIC src/mylibrary.c)

# 添加库的头文件路径
target_include_directories(MyProject PUBLIC include)

# 添加依赖库
target_link_libraries(MyProject PRIVATE MyLibrary)
```

这个示例中，我们创建了一个基于C语言的名为"MyProject"的项目，设置了C11标准，并添加了一个名为"MyLibrary"的静态库。我们还指定了源文件、头文件以及库的依赖关系。

CMakeLists.txt文件包含以下几种主要语法：

1. 注释：以`#`开头的行表示注释。

   示例：
   ```cmake
   # 这是一条注释
   ```

2. 变量：使用`set()`函数设置变量值，通过`${}`使用变量值。

   示例：
   ```cmake
   set(SOURCE_FILES main.cpp)
   add_executable(my_app ${SOURCE_FILES})
   ```

3. 条件控制：使用`if()`, `elseif()`, `else()` 和 `endif()`来进行条件控制。

   示例：
   ```cmake
   if(WIN32)
      set(PLATFORM "Windows")
   elseif(APPLE)
      set(PLATFORM "macOS")
   elseif(UNIX)
      set(PLATFORM "Linux")
   else()
      message(FATAL_ERROR "Unsupported platform")
   endif()
   ```

4. 循环：使用`foreach()` 相关语法进行循环。

   示例：
   ```cmake
   set(SOURCE_FILES main.cpp file1.cpp file2.cpp)
   foreach(file ${SOURCE_FILES})
      message("source file: ${file}")
   endforeach()
   ```

5. 函数和宏：使用`function()`和`endfunction()`定义函数，使用`macro()`和`endmacro()`定义宏。

   示例：
   ```cmake
   function(print_files files)
      foreach(file ${files})
         message("file: ${file}")
      endforeach()
   endfunction()

   macro(my_macro arg1 arg2)
      message("arg1 is ${arg1}, arg2 is ${arg2}")
   endmacro()

   set(SOURCE_FILES main.cpp file1.cpp file2.cpp)
   print_files(${SOURCE_FILES})
   my_macro("Hello" "World")
   ```

## 示例

下面用一段示例来综合下上面的语法：

```cmake
# 设置CMake最低版本要求
cmake_minimum_required(VERSION 3.0)

# 创建项目hello_world
project(hello_world)

# 设置C++11标准
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 检测平台类型
if(WIN32)
    set(PLATFORM "Windows")
elseif(APPLE)
    set(PLATFORM "macOS")
elseif(UNIX)
    set(PLATFORM "Linux")
else()
    # 如果平台不支持，给出错误提示
    message(FATAL_ERROR "Unsupported platform")
endif()

# 定义一个函数print_files，用于打印文件列表
function(print_files)
    foreach(file ${ARGN})
        message("file: ${file}")
    endforeach()
endfunction()

# 定义一个宏my_macro，用于打印平台和处理器信息
macro(my_macro arg1 arg2)
    message("PLATFORM is ${arg1}, CMAKE_HOST_SYSTEM_PROCESSOR is ${arg2}")
endmacro()

# 设置文件列表
set(files main.cpp file1.cpp file2.cpp)

# 调用print_files函数，打印文件列表
print_files(${files})

# 调用my_macro宏，打印平台和处理器信息
my_macro(${PLATFORM} ${CMAKE_HOST_SYSTEM_PROCESSOR})

# 添加可执行文件hello_world
add_executable(hello_world main.cpp)
```
这是一个基本的CMakeLists.txt文件，主要包含以下内容：

1. 设置CMake的最低版本要求为3.0。
2. 创建一个名为hello_world的项目。
3. 设定C++11标准。
4. 检测当前平台类型（Windows、macOS或Linux），并将其存储在变量PLATFORM中。
5. 定义一个名为print_files的函数，用于打印文件列表。
6. 定义一个名为my_macro的宏，用于打印平台和处理器信息。
7. 设置一个文件列表，并使用print_files函数打印出来。
8. 调用my_macro宏，打印平台和处理器信息。
9. 添加一个名为hello_world的可执行文件，其源代码为main.cpp。

运行结果：

```powershell
esp-test\hello\build via △ v3.24.3 
❯ cmake ..
-- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19045.
file: main.cpp
file: file1.cpp
file: file2.cpp
PLATFORM is Windows, CMAKE_HOST_SYSTEM_PROCESSOR is AMD64
-- Configuring done
-- Generating done
-- Build files have been written to: C:/Users/yp.wang/Desktop/esp-test/hello/build
```

虽然基本语法并不复杂，但组合使用也是变化繁多，再加上还有各种内置变量，实际大型项目中的`CMakeLists.txt`可能非常复杂，需要持续认真学习和积累。好在cmake语法相对简单，比较接近自然语言，所以实际读起来还是很容易理解的。
