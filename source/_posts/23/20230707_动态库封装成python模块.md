---
title: 动态库封装成python模块
date: 2023-07-07 16:25:11
categories:
tags:
    - python
    - 嵌入式
    - 工具
---

之前的文章 [将静态库封装成 python 模块](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481679&idx=1&sn=5a6594f9f4fd4b5331fed49a6d83938a&chksm=84ad708cb3daf99a0461473c12a95f40614c92c3ebe914353f7c5f962aa62455df5a9c68e9bd#rd) 中讲解了如何将静态库封装成python模块，静态库封装相对来说还是有点复杂，今天来介绍下动态库封装成python模块的方法。

## 主要方法

将动态库封装成Python的主要方法有以下几种：

1. 使用ctypes库：ctypes是Python标准库中用于调用动态库的工具库。它提供了一组用于描述C数据类型和调用C函数的功能。你可以使用ctypes库来加载动态库并调用其中的函数、访问结构体等。通过定义C函数的原型和C结构体的映射，可以方便地与动态库进行交互。

2. 使用Cython：Cython是一个Python的扩展语法，允许你编写使用C语言语法的Python扩展模块。通过使用Cython，你可以将动态库中的函数包装为Python的可调用函数，并且可以直接在Python代码中使用这些函数，而无需编写额外的C代码。

3. 使用SWIG：SWIG（Simplified Wrapper and Interface Generator）是一个自动化工具，用于生成连接C/C++和其他高级语言的接口代码。它可以根据给定的接口描述文件自动生成Python的封装代码。SWIG支持多种语言，包括Python和C/C++，使得将动态库封装成Python模块变得更加简单和高效。

哈哈，其实跟封装静态库差不多，用Cython或SWIG，既能封装静态库，也能封装动态库，但我们今天主要用`ctypes`，简便、主流。

<!-- more -->

## [`ctypes`](https://docs.python.org/3/library/ctypes.html)

当使用`ctypes`封装动态库时，以下是一般的步骤和方法：

1. 导入`ctypes`模块：首先，在Python脚本中导入`ctypes`模块，以便使用其中的功能。例如：
   
```python
import ctypes
```

2. 加载动态库：使用`ctypes`提供的函数加载动态库文件。有几种方法可以加载动态库：
   
- 使用`cdll`加载动态库：
  
```python
mylib = ctypes.CDLL("mylib.so")  # Linux/macOS
# 或者
mylib = ctypes.CDLL("mylib.dll")  # Windows
```

- 使用`windll`加载Windows动态库：

```python
mylib = ctypes.windll.LoadLibrary("mylib.dll")
```

- 使用`oledll`加载Windows OLE动态库：

```python
mylib = ctypes.oledll.LoadLibrary("mylib.dll")
```

在加载动态库后，可以通过`mylib`对象访问其中定义的函数和变量。

3. 定义函数原型：为了能够正确地调用动态库中的函数，需要在Python中定义与动态库函数相对应的函数原型。可以使用`ctypes`提供的`CFUNCTYPE`函数来定义函数原型，指定参数类型和返回类型。例如：

```python
my_function = mylib.my_function
my_function.argtypes = [ctypes.c_int, ctypes.c_float]
my_function.restype = ctypes.c_void_p
```

在上述示例中，`my_function`是动态库中的函数名，`argtypes`属性指定函数的参数类型列表，`restype`属性指定函数的返回类型。

4. 调用动态库函数：使用Python中定义的函数原型来调用动态库中的函数。可以直接像调用普通函数一样调用动态库函数，传递相应的参数即可。例如：

```python
result = my_function(10, 3.14)
```

在调用函数时，确保传递正确的参数类型和顺序，以及处理返回值的类型和内容。

5. 使用动态库中的结构体：如果动态库中定义了结构体，可以在Python中使用`ctypes`定义相应的结构体，以便与动态库进行交互。使用`ctypes`提供的`Structure`类来定义结构体，并指定其成员变量的类型和顺序。例如：

```python
class MyStruct(ctypes.Structure):
    _fields_ = [("field1", ctypes.c_int),
                ("field2", ctypes.c_float)]
```

在上述示例中，`MyStruct`是自定义的结构体名称，`_fields_`属性指定了结构体的成员变量和类型。

可以使用定义好的结构体来传递参数或接收动态库函数的返回值。

这是使用`ctypes`封装动态库的基本方法。根据具体的动态库和需求，可能需要更多的配置和处理，例如处理指针、处理字符串、处理数组等。`ctypes`提供了丰富的功能和类型，可以根据需要进行进一步的学习和使用。

## 实例

前面的文章 [动态库封装成 python 模块](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481692&idx=1&sn=ee391f11e05337f6a7d304f618ebea62&chksm=84ad709fb3daf9894c933dab44458e8c4b07ecce9da9fdc4ce13a79feef6e9cc2777ad4b4ef4#rd) 介绍了将动态库封装成python模块的方法，今天就以最近在弄的`ch347`芯片的驱动为例，实践下。

沁恒在[ch347](https://www.wch.cn/products/CH347.html)的资料页并没有提供ch347驱动的动态库，而是在`CH347EVT.ZIP`中提供了静态库。后来在跑demo时提示找不到动态库，搜了一下，原来这个动态库在`CH341PAR.ZIP`里。沁恒提供的驱动库文件主要有以下几个：

- `CH347DLL.H`: 驱动库中文头文件
- `CH347DLL_EN.H`: 驱动库英文头文件
- `CH347DLL.LIB`: 32位静态库
- `CH347DLLA64.LIB`: 64位静态库
- `CH347DLL.DLL`: 32位动态库
- `CH347DLLA64.DLL`: 64位动态库

下面以`CH347OpenDevice(ULONG DevI)`, `CH347CloseDevice(ULONG iIndex)`, `CH347GetDeviceInfor(ULONG iIndex,mDeviceInforS *DevInformation)`, `CH347GetVersion(ULONG iIndex, PUCHAR iDriverVer,	PUCHAR iDLLVer, PUCHAR ibcdDevice, PUCHAR iChipType)`4个函数的封装为例。

首先新建项目文件夹`ch347-py`，

```powershell
mkdir ch347-py && cd ch347-py
```

然后将驱动库文件复制到项目文件夹中，因为今天是以动态库进行封装，所以只复制dll文件即可，头文件复制过来方便查看库函数声明和数据结构。注意动态库的位数要跟系统和python对应，不确定的话可以32位和64位都放进来，后面试一下再确定用哪个版本。

接着用vs code打开项目文件夹，并新建一个`ch347.py`文件，写入以下内容：

```python
import ctypes


# Load the CH347DLL library
ch347dll = ctypes.WinDLL('CH347DLLA64.dll')  # Update the filename if necessary

# Define the argument and return types for CH347OpenDevice
ch347dll.CH347OpenDevice.argtypes = [ctypes.c_ulong]
ch347dll.CH347OpenDevice.restype = ctypes.c_bool

# Define the argument and return types for CH347CloseDevice
ch347dll.CH347CloseDevice.argtypes = [ctypes.c_ulong]
ch347dll.CH347CloseDevice.restype = ctypes.c_bool

# Define the argument and return types for CH347GetDeviceInfor
MAX_PATH = 260

class mDeviceInforS(ctypes.Structure):
    _fields_ = [("iIndex", ctypes.c_ubyte),
                ("DevicePath", ctypes.c_char * MAX_PATH),
                ("UsbClass", ctypes.c_ubyte),
                ("FuncType", ctypes.c_ubyte),
                ("DeviceID", ctypes.c_char * 64),
                ("ChipMode", ctypes.c_ubyte),
                ("DevHandle", ctypes.c_void_p),
                ("BulkOutEndpMaxSize", ctypes.c_ushort),
                ("BulkInEndpMaxSize", ctypes.c_ushort),
                ("UsbSpeedType", ctypes.c_ubyte),
                ("CH347IfNum", ctypes.c_ubyte),
                ("DataUpEndp", ctypes.c_ubyte),
                ("DataDnEndp", ctypes.c_ubyte),
                ("ProductString", ctypes.c_char * 64),
                ("ManufacturerString", ctypes.c_char * 64),
                ("WriteTimeout", ctypes.c_ulong),
                ("ReadTimeout", ctypes.c_ulong),
                ("FuncDescStr", ctypes.c_char * 64),
                ("FirewareVer", ctypes.c_ubyte)]

ch347dll.CH347GetDeviceInfor.argtypes = [ctypes.c_ulong, ctypes.POINTER(mDeviceInforS)]
ch347dll.CH347GetDeviceInfor.restype = ctypes.c_bool

# Define the argument and return types for CH347GetVersion
ch347dll.CH347GetVersion.argtypes = [
    ctypes.c_ulong,
    ctypes.POINTER(ctypes.c_ubyte),
    ctypes.POINTER(ctypes.c_ubyte),
    ctypes.POINTER(ctypes.c_ubyte),
    ctypes.POINTER(ctypes.c_ubyte)
]
ch347dll.CH347GetVersion.restype = ctypes.c_bool

def OpenDevice(DevI):
    # Call the CH347OpenDevice function
    return ch347dll.CH347OpenDevice(DevI)

def CloseDevice(iIndex):
    # Call the CH347CloseDevice function
    return ch347dll.CH347CloseDevice(iIndex)

def GetDeviceInfor(iIndex):
    # Create an instance of mDeviceInforS structure
    dev_info = mDeviceInforS()

    # Call the CH347GetDeviceInfor function
    result = ch347dll.CH347GetDeviceInfor(iIndex, ctypes.byref(dev_info))

    # Return the result and device information
    return result, dev_info

def GetVersion(iIndex):
    # Create variables to store the version information
    driver_ver = ctypes.c_ubyte()
    dll_ver = ctypes.c_ubyte()
    device_ver = ctypes.c_ubyte()
    chip_type = ctypes.c_ubyte()

    # Call the CH347GetVersion function
    result = ch347dll.CH347GetVersion(iIndex,
                                      ctypes.byref(driver_ver),
                                      ctypes.byref(dll_ver),
                                      ctypes.byref(device_ver),
                                      ctypes.byref(chip_type))

    # Return the result and version information
    return result, driver_ver.value, dll_ver.value, device_ver.value, chip_type.value
```

这样就将动态库中的4个函数封装好了。接下来新建一个测试文件`test.py`，写入以下内容：

```python
import ch347


# Usage
device_index = 0  # Set the device index according to your requirements

result = ch347.OpenDevice(device_index)
if result:
    print(f"Successfully opened device index: {device_index}")
else:
    print(f"Failed to close device index: {device_index}")

# Example usage of CH347GetDeviceInfor
result, device_info = ch347.GetDeviceInfor(device_index)
if result:
    print("Device Information:")
    print(f"iIndex: {device_info.iIndex}")
    print(f"DevicePath: {device_info.DevicePath.decode()}")
    print(f"UsbClass: {device_info.UsbClass}")
    print(f"FuncType: {device_info.FuncType}")
    print(f"DeviceID: {device_info.DeviceID.decode()}")
    print(f"ChipMode: {device_info.ChipMode}")
    print(f"DevHandle: {device_info.DevHandle}")
    print(f"BulkOutEndpMaxSize: {device_info.BulkOutEndpMaxSize}")
    print(f"BulkInEndpMaxSize: {device_info.BulkInEndpMaxSize}")
    print(f"UsbSpeedType: {device_info.UsbSpeedType}")
    print(f"CH347IfNum: {device_info.CH347IfNum}")
    print(f"DataUpEndp: {device_info.DataUpEndp}")
    print(f"DataDnEndp: {device_info.DataDnEndp}")
    print(f"ProductString: {device_info.ProductString.decode()}")
    print(f"ManufacturerString: {device_info.ManufacturerString.decode()}")
    print(f"WriteTimeout: {device_info.WriteTimeout}")
    print(f"ReadTimeout: {device_info.ReadTimeout}")
    print(f"FuncDescStr: {device_info.FuncDescStr.decode()}")
    print(f"FirewareVer: {device_info.FirewareVer}")
    print(repr(device_info))
else:
    print("Failed to get device information.")
    
# Example usage of CH347GetVersion
result, driver_ver, dll_ver, device_ver, chip_type = ch347.GetVersion(device_index)
if result:
    print("Version Information:")
    print(f"Driver Version: {driver_ver}")
    print(f"DLL Version: {dll_ver}")
    print(f"Device Version: {device_ver}")
    print(f"Chip Type: {chip_type}")
else:
    print("Failed to get version information.")

# Example usage of CH347CloseDevice
result = ch347.CloseDevice(device_index)
if result:
    print(f"Successfully closed device index: {device_index}")
else:
    print(f"Failed to close device index: {device_index}")
```

连接ch347板子，运行一下：

```powershell
❯  python test.py 
Successfully opened device index: 0
Device Information:
iIndex: 0
DevicePath: \\?\USB#VID_1A86&PID_55DB&MI_02#7&2887B016&0&0002#{5446F048-98B4-4EF0-96E8-27994BAC0D00}
UsbClass: 0
FuncType: 1
DeviceID: USB\VID_1A86&PID_55DB&MI_02#7&2887B016&0&0002#
ChipMode: 1
DevHandle: 884
BulkOutEndpMaxSize: 512
BulkInEndpMaxSize: 512
UsbSpeedType: 1
CH347IfNum: 0
DataUpEndp: 6
DataDnEndp: 6
ProductString:
ManufacturerString:
WriteTimeout: 500
ReadTimeout: 500
FuncDescStr: USB2.0 To SPI&IIC
FirewareVer: 64
<ch347_driver.mDeviceInforS object at 0x00000215AA0932C0>
Version Information:
Driver Version: 35
DLL Version: 34
Device Version: 64
Chip Type: 1
Successfully closed device index: 0
```

![ch347高速usb桥接板](https://imgs.boringhex.top/blog/微信图片_20230705192828.jpg)
