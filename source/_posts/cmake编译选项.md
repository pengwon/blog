---
title: cmake编译选项
date: 2023-06-25 12:08:03
categories:
tags:
    - esp32
    - cmake
    - ci/cd
---

CMake是一个跨平台的构建系统，它可以根据简单的配置文件生成各种平台的构建工具（例如Makefile、Visual Studio项目文件等）。CMake使用CMakeLists.txt文件来描述项目的构建规则和依赖关系。在这个文件中，可以设置各种编译选项。

1. 设置C++标准：

   ```cmake
   set(CMAKE_CXX_STANDARD 11)
   set(CMAKE_CXX_STANDARD_REQUIRED ON)
   ```

   这里设置了项目使用C++11标准进行编译，并且如果编译器不支持C++11，CMake会报错。

2. 设置编译器选项：

   ```cmake
   if (MSVC)
       add_compile_options(/W4 /WX)
   else()
       add_compile_options(-Wall -Wextra -Werror)
   endif()
   ```

   这个示例为不同的编译器设置了不同的编译选项。对于Microsoft Visual Studio编译器，它将警告级别设置为/W4并将所有警告视为错误。对于其他编译器（如GCC、Clang），它将启用所有警告、额外警告，并将警告视为错误。

<!-- more -->

1. 添加定义：

   ```cmake
   add_definitions(-DENABLE_DEBUG)
   ```

   这将为项目添加一个宏定义`ENABLE_DEBUG`。在C++代码中，可以使用`#ifdef ENABLE_DEBUG`检查宏是否定义。

2. 设置优化选项：

   ```cmake
   set(CMAKE_BUILD_TYPE Release)
   ```

   这将设置构建类型为"Release"，通常表示编译器会启用优化选项。CMake支持多种构建类型，如Debug、Release、RelWithDebInfo和MinSizeRel。

3. 设置项目输出目录：

   ```cmake
   set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
   set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
   set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
   ```

   这将设置项目的可执行文件、库文件和静态库文件的输出目录。

4. 设置依赖库：

   ```cmake
   find_package(Threads REQUIRED)
   target_link_libraries(my_project PRIVATE Threads::Threads)
   ```

   这个示例会在系统上查找线程库（例如POSIX Threads）。如果找到了线程库，它会链接到名为`my_project`的目标。

   这些仅仅是CMake设置编译选项的一些基本示例。CMake提供了许多配置选项和命令，可以根据项目的需求进行灵活的定制。有关CMake的更多信息，请参阅[CMake官方文档](https://cmake.org/documentation/)。

基于esp-idf开发esp32时，idf.py工具对cmake做了一些封装，但基本兼容，cmake具有的子命令，idf.py基本都有。

下面是esp-idf开发esp32常用的CMakeLists.txt示例：

1. 在项目的根目录下创建一个名为`CMakeLists.txt`的文件。这个文件将包含项目的编译选项和构建规则。以下是一个简单的示例：

   ```cmake
   # 设置CMake的最低版本要求
   cmake_minimum_required(VERSION 3.5)

   # 包含ESP-IDF的CMake工具链文件
   include($ENV{IDF_PATH}/tools/cmake/project.cmake)

   # 设置项目名称和项目中的源文件
   project(my_project)
   ```

2. 在项目的组件目录下（通常为`components`）创建一个名为`CMakeLists.txt`的文件。这个文件将包含组件的编译选项和构建规则。以下是一个简单的示例：

   ```cmake
   # 注册组件
   idf_component_register(SRCS "my_component.c"
                          INCLUDE_DIRS "include")
   ```

3. 设置编译选项：在 `CMakeLists.txt` 文件中，使用 `target_compile_options` 函数来设置编译选项。例如，要启用C++11支持，可以添加以下代码：

   ```cmake
   set(CMAKE_CXX_STANDARD 11)
   set(CMAKE_CXX_STANDARD_REQUIRED ON)
   ```

4. 使用 `idf.py` 工具进行编译和构建。在项目根目录下，运行以下命令：

   ```
   idf.py build
   ```

   这将使用CMake和ESP-IDF工具链编译项目，并生成二进制文件。

5. 如果需要更改默认编译选项，可以在`CMakeLists.txt`中使用`target_compile_definitions`，`target_compile_features`等函数。例如：

   ```cmake
   # 添加编译定义
   target_compile_definitions(${COMPONENT_TARGET} PRIVATE MY_DEFINE=value)

   # 添加编译选项
   target_compile_options(${COMPONENT_TARGET} PRIVATE -Werror)
   ```

参考文档：
- [ESP-IDF CMake编译系统](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-guides/build-system.html)
- [CMake文档](https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html)
