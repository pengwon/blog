---
title: 将静态库封装成python模块
date: 2023-07-04 10:09:39
categories:
tags:
    - python
    - 嵌入式
    - 工具
---

很多硬件厂商的底层设备驱动都是以库的形式提供给开发者，有的是动态库，有的是静态库。开发上层应用，最快速便捷的方式当然还是用python，对于动态库，可以用python的ctypes库进行加载，而对于静态库，则要麻烦一些，今天折腾了很长时间，总算跑通了最简流程。

## 主要方法

静态库（LIB）是在编译链接阶段被静态地链接到程序中的，因此无法直接在 Python 中调用。Python 只能直接调用动态链接库（DLL）。

如果想在 Python 中使用静态库的功能，主要有以下几种方法：

1. 将静态库转换为动态库：可以通过使用相应的编译器（例如，Visual Studio 或 GCC）将静态库转换为动态库（DLL）。这样就可以直接在 Python 中调用动态库了。

2. 使用 C/C++ 扩展模块：可以使用 Python 的 C/C++ 扩展机制，将静态库中的函数封装为 C/C++ 扩展模块，然后在 Python 中调用这些模块。通过这种方式，可以直接在 Python 中使用静态库的功能。

3. 使用第三方工具：有些第三方工具可以帮助将静态库转换为 Python 可用的动态库或 C/C++ 扩展模块。例如，Cython 可以将 C/C++ 代码转换为 Python 可执行的代码；SWIG 可以生成用于 Python 的 C/C++ 接口代码。

需要注意的是，将静态库转换为动态库或者创建 C/C++ 扩展模块都需要一定的 C/C++ 编程经验。如果你不熟悉 C/C++，可以考虑使用其他方法解决问题，如寻找已经有 Python 绑定的动态库或者使用其他库或工具实现相同的功能。

<!-- more -->

## [Cython](https://cython.org/)

今天主要尝试了用Cython将静态库封装成python模块。

首先新建一个文件夹用作今天的操场：

```powershell
# 以 lib_test 为例
mkdir lib_test && cd lib_test
```

在`lib_test`目录下新建`my_static_lib.h`和`my_static_lib.cpp`两个文件，并分别写入以下内容： 

```c++
// my_static_lib.h
#ifndef MY_STATIC_LIB_H
#define MY_STATIC_LIB_H

#ifdef __cplusplus
extern "C" {
#endif

void function1();
void function2();
void function3();
void function4();
void function5();
void function6();
void function7();
void function8();
void function9();
void function10();

#ifdef __cplusplus
}
#endif

#endif
```

```c++ 	
// my_static_lib.cpp
#include <iostream>
#include "my_static_lib.h"

void function1()
{
    std::cout << "This is function 1." << std::endl;
}

void function2()
{
    std::cout << "This is function 2." << std::endl;
}

void function3()
{
    std::cout << "This is function 3." << std::endl;
}

void function4()
{
    std::cout << "This is function 4." << std::endl;
}

void function5()
{
    std::cout << "This is function 5." << std::endl;
}

void function6()
{
    std::cout << "This is function 6." << std::endl;
}

void function7()
{
    std::cout << "This is function 7." << std::endl;
}

void function8()
{
    std::cout << "This is function 8." << std::endl;
}

void function9()
{
    std::cout << "This is function 9." << std::endl;
}

void function10()
{
    std::cout << "This is function 10." << std::endl;
}
```

然后编写`CMakeLists.txt`文件：

```cmake
cmake_minimum_required(VERSION 3.0)
project(my_static_library)

# 设置编译器为C++
set(CMAKE_CXX_STANDARD 11)

# 添加静态库的源文件
add_library(my_static_lib STATIC
    my_static_lib.cpp
)
```

构建生成静态库：

```powershell
# 新建 build 目录
mkdir build && cd build

# 生成 vs 解决方案
cmake ..

# 构建
cmake --build .
```

安装`Cython`：

```powershell
pip install cython
```

在`lib_test`目录下新建`my_static_lib.pyx`和`setup.py`两个文件，并写入以下内容：

```python
# my_static_lib.pyx
cdef extern from "my_static_lib.h":
    void function1()
    void function2()
    void function3()
    void function4()
    void function5()
    void function6()
    void function7()
    void function8()
    void function9()
    void function10()

def py_function1():
    function1()

def py_function2():
    function2()

def py_function3():
    function3()

def py_function4():
    function4()

def py_function5():
    function5()

def py_function6():
    function6()

def py_function7():
    function7()

def py_function8():
    function8()

def py_function9():
    function9()

def py_function10():
    function10()
```

```python 
# setup.py
from setuptools import Extension, setup
from Cython.Build import cythonize

setup(
    ext_modules=cythonize(Extension("my_static_lib", ["my_static_lib.pyx"], extra_link_args=["/LIBPATH:PATH\\to\\my_static_lib.lib"], libraries=["my_static_lib"]))
)
```

注意将示例中的`PATH\\to\\my_static_lib.lib`替换为实际的静态库所在**绝对**路径。

然后执行：

```powershell
# 回到 lib_test 目录下
cd ..
python .\setup.py build_ext -i
```

测试一下：

```powershell
❯ python
Python 3.8.10 (tags/v3.8.10:3d8993a, May  3 2021, 11:48:03) [MSC v.1928 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import my_static_lib
>>> my_static_lib.py_function()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'my_static_lib' has no attribute 'py_function'
>>> my_static_lib.py_function1()
This is function 1.
>>> my_static_lib.py_function5()
This is function 5.
>>> my_static_lib.py_function10()
This is function 10.
```
