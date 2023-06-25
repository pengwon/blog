---
title: cmake函数语法
date: 2023-06-25 11:54:18
categories:
tags:
    - esp32
    - cmake
    - ci/cd
---

在CMake中，函数用于封装一段可重用的代码。下面是CMake函数的定义和调用的详细讲解。

## 定义函数

函数的定义使用`function()`来开始，然后指定函数名称以及任何形参列表，如下所示：

```cmake
function(<function_name> [arg1 [arg2 [...]]])
   # Function code...
endfunction()
```

函数名称应该是唯一的，以避免与其他函数发生冲突。关键字`function()`与`endfunction()`之间的代码是函数代码块。

这里的形参列表是可选的，您可以为您的函数定义零个、一个或多个参数。注意，函数调用中的参数列表是以空格分隔的，因此每个参数将自动映射到定义中的参数。

<!-- more -->

## 使用ARGN与ARGC

CMake中的两个特殊变量`ARGN`和`ARGC`对于处理函数参数尤为有用。

- ARGN: 该变量包含函数调用时传递的所有未指定名称的参数。这在处理不确定数量的参数时很有用。
- ARGC: 该变量包含传递给函数的参数总数。

## 调用函数

调用CMake函数时，只需使用函数名称并传递所需的参数。在调用函数时，在实际参数列表中定义的参数名称将被绑定到调用函数时传递的参数值。

```cmake
<function_name>([arg1 [arg2 [...]]])
```

## 示例：

```cmake
# 定义一个简单的函数，接收两个参数并输出它们
function(print_args ARG1 ARG2)
    message("Argument 1 is '${ARG1}'")
    message("Argument 2 is '${ARG2}'")
endfunction()

# 调用print_args函数
print_args("Hello" "World")
# 输出：
# Argument 1 is 'Hello'
# Argument 2 is 'World'

# 一个处理不确定参数量的函数示例
function(print_multiple_args)
    set(NumArgs ${ARGC})
    message("Total number of arguments: ${NumArgs}")
    foreach(Arg ${ARGN})
        message("Arg: '${Arg}'")
    endforeach()
endfunction()

# 调用print_multiple_args函数
print_multiple_args("arg1" "arg2" "arg3")
# 输出：
# Total number of arguments: 3
# Arg: 'arg1'
# Arg: 'arg2'
# Arg: 'arg3'
```

在电脑上实操下将有助于更好地理解CMake函数以及如何定义和调用它们。
