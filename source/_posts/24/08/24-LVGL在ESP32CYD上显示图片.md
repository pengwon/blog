---
title: LVGL在ESP32 CYD上显示图像
date: 2024-08-24 11:24:53
categories:
tags:
    - 嵌入式
---


## ESP32 CYD 使用 LVGL 在屏幕上显示图像 | Random Nerd Tutorials

在本指南中，你将学习如何使用LVGL在ESP32 Cheap Yellow Display (CYD) 板上处理和加载图像。ESP32将使用Arduino IDE进行编程。

**对ESP32 Cheap Yellow Display不熟悉？** 从这里开始：[开始使用ESP32 Cheap Yellow Display Board – CYD (ESP32-2432S028R)](https://randomnerdtutorials.com/cheap-yellow-display-esp32-2432s028r/)。

## 项目概述

在这个项目中，你将学习如何使用LVGL在CYD上显示图像。我们将创建一个示例来显示如下图片所示的猫的图像，但你可以根据需要加载任何其他图像。

![CAT](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/08/ESP32-CYD-with-LVGL-Display-Image-on-the-screen-demonstration.png?w=750&quality=100&strip=all&ssl=1)

<!--more-->

## 前提条件

在继续之前，请确保你遵循了以下前提条件。**你必须遵循所有步骤，否则你的项目将无法工作。**

### 1) 所需部件

对于这个项目，你需要以下部件：
- ESP32-2432S028R – 2.8英寸240×320智能显示屏TFT带触摸屏

### 2) 在Arduino IDE中安装ESP32板

我们将使用Arduino IDE对ESP32进行编程。确保你已经安装了ESP32板。请按照以下教程操作：
- [在Arduino IDE 2中安装ESP32板（Windows, Mac OS X, Linux）](https://randomnerdtutorials.com/installing-esp32-arduino-ide-2-0/)

### 3) 熟悉ESP32 Cheap Yellow Display

ESP32-2432S028R开发板在创客社区被称为“Cheap Yellow Display”或简称CYD。这个开发板的主要芯片是ESP32-WROOM-32模块，配备了2.8英寸TFT触摸屏LCD、microSD卡接口、RGB LED以及编程和为板供电所需的所有电路。

![ESP32 CYD](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/03/ESP32-Cheap-Yellow-Display-CYD-Board-ESP32-2432S028R-front.jpg?w=750&quality=100&strip=all&ssl=1)

如果你第一次使用ESP32 Cheap Yellow Display，请确保遵循我们的入门指南：
- [开始使用ESP32 Cheap Yellow Display Board和LVGL](https://randomnerdtutorials.com/lvgl-cheap-yellow-display-esp32-2432s028r/)

### 4) 安装SPI和LVGL库

LVGL（轻量级多功能图形库）是一个免费且开源的图形库，为你的微控制器项目提供了广泛的易于使用的图形元素，这些项目需要图形用户界面（GUI）。

![LVGL](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/08/LVGL-new-logo.png?w=622&quality=100&strip=all&ssl=1)

按照以下教程安装和配置使用ESP32 Cheap Yellow Display的Arduino IDE所需的库。
- [**开始使用ESP32 Cheap Yellow Display Board (ESP32-2432S028R)上的LVGL**](https://randomnerdtutorials.com/lvgl-cheap-yellow-display-esp32-2432s028r/)

## 准备图像文件 – _image.h_

要使用LVGL加载自定义图像，你需要创建和配置一个名为_image.h_的文件，该文件必须放置在草图文件夹中。我们已经为你准备了这个文件。如果你想加载自定义图像，你只需要替换图像`uint8_t`映射和图像大小。

你可以使用我们的示例图像或按照以下步骤准备自定义图像：

1. 访问LVGL图像转换器网站：[lvgl.io/tools/imageconverter](https://lvgl.io/tools/imageconverter)
2. 选择以下图片中突出显示的选项：
   - 版本：LVGL v9
   - 从你的计算机中选择一个图像
   - 颜色格式：ARGB8888
   - 然后，点击**转换**按钮
    ![LVGL Image Converter](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/08/LVGL-Image-Converter-LVGL-v9-ARGB8888.png?w=708&quality=100&strip=all&ssl=1)
3. 它将下载一个以你的图片名称命名但具有`.c`扩展名的文件。打开该文件，复制括号`{}`内的代码，其中包含许多十六进制字符。
4. 创建一个名为 _image.h_ 的新文件。
5. [**将此存储库中的代码复制**](https://raw.githubusercontent.com/RuiSantosdotme/ESP32-TFT-Touchscreen/main/examples/display-images/image.h)并粘贴到你刚刚创建的新的 _image.h_ 文件中。
6. 用你在第3步中复制的内容替换`my_image_map`变量的内容。（**不要更改**`my_image_map`变量的名称，只复制和替换括号内的内容）。
```h
#ifdef __has_include
    #if __has_include("lvgl.h")
        #ifndef LV_LVGL_H_INCLUDE_SIMPLE
            #define LV_LVGL_H_INCLUDE_SIMPLE
        #endif
    #endif
#endif

#if defined(LV_LVGL_H_INCLUDE_SIMPLE)
    #include "lvgl.h"
#else
    #include "lvgl/lvgl.h"
#endif

#define LV_BIG_ENDIAN_SYSTEM

#ifndef LV_ATTRIBUTE_MEM_ALIGN
#define LV_ATTRIBUTE_MEM_ALIGN
#endif

#ifndef LV_ATTRIBUTE_IMG_MY_IMAGE
#define LV_ATTRIBUTE_IMG_MY_IMAGE
#endif

const LV_ATTRIBUTE_MEM_ALIGN LV_ATTRIBUTE_IMG_MY_IMAGE uint8_t my_image_map[] = {REPLACE_WITH_YOUR__IMAGE_ATTRIBUTE};

const lv_image_dsc_t my_image = {
    .header = {
        .magic = LV_IMAGE_HEADER_MAGIC,
        .cf = LV_COLOR_FORMAT_ARGB8888,
        .flags = 0,          
        .w = 128,
        .h = 128,
        //.stride = 120,
        .reserved_2 = 0
    },
    .data_size = sizeof(my_image_map),
    .data = my_image_map,
    .reserved = NULL
};
```
7. 滚动到该文件底部，使用你的图像宽度`.w`和高度`.h`更改以下行：
```h
const lv_image_dsc_t my_image = {
    .header = {
        .magic = LV_IMAGE_HEADER_MAGIC,
        .cf = LV_COLOR_FORMAT_ARGB8888,
        .flags = 0,          
        .w = 128,
        .h = 128,
        //.stride = 120,
        .reserved_2 = 0
    },
    .data_size = sizeof(my_image_map),
    .data = my_image_map,
    .reserved = NULL
};
```
8. 最后，保存你的 _image.h_ 文件。

**重要：** _image.h_ 文件应放在你的项目的草图文件夹中的 _.ino_ 文件旁边。

![相同目录](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/08/LVGL-Load-Image-Arduino-Code-example-folder.png?resize=267%2C282&quality=100&strip=all&ssl=1)

你的Arduino IDE应该有以下两个标签：

![两个标签页](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/08/Arduino-IDE-tabs-LVGL-Load-Image-Arduino-Code-example-folder.png?w=406&quality=100&strip=all&ssl=1)

## ESP32 CYD: 加载和显示图像 – Arduino代码

以下代码将在屏幕上显示图像。你必须事先准备好 _image.h_ ，其中包含你选择的图像。或者，你可以使用我们的默认 _image.h_ 文件。该文件应放置在草图文件夹中，紧挨着 _.ino_ 文件。

```c
/* Rui Santos & Sara Santos - Random Nerd Tutorials - https://RandomNerdTutorials.com/esp32-cyd-lvgl-display-image/
   THIS EXAMPLE WAS TESTED WITH THE FOLLOWING HARDWARE:
   1) ESP32-2432S028R 2.8 inch 240×320 also known as the Cheap Yellow Display (CYD): https://makeradvisor.com/tools/cyd-cheap-yellow-display-esp32-2432s028r/
      SET UP INSTRUCTIONS: https://RandomNerdTutorials.com/cyd-lvgl/
   2) REGULAR ESP32 Dev Board + 2.8 inch 240x320 TFT Display: https://makeradvisor.com/tools/2-8-inch-ili9341-tft-240x320/ and https://makeradvisor.com/tools/esp32-dev-board-wi-fi-bluetooth/
      SET UP INSTRUCTIONS: https://RandomNerdTutorials.com/esp32-tft-lvgl/
   Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files.
   The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
*/

/* Install the "lvgl" library version 9.X by kisvegabor to interface with the TFT Display - https://lvgl.io/
   *** IMPORTANT: lv_conf.h available on the internet will probably NOT work with the examples available at Random Nerd Tutorials ***
   *** YOU MUST USE THE lv_conf.h FILE PROVIDED IN THE LINK BELOW IN ORDER TO USE THE EXAMPLES FROM RANDOM NERD TUTORIALS ***
   FULL INSTRUCTIONS AVAILABLE ON HOW CONFIGURE THE LIBRARY: https://RandomNerdTutorials.com/cyd-lvgl/ or https://RandomNerdTutorials.com/esp32-tft-lvgl/  */
#include <lvgl.h>

/* Install the "TFT_eSPI" library by Bodmer to interface with the TFT Display - https://github.com/Bodmer/TFT_eSPI
   *** IMPORTANT: User_Setup.h available on the internet will probably NOT work with the examples available at Random Nerd Tutorials ***
   *** YOU MUST USE THE User_Setup.h FILE PROVIDED IN THE LINK BELOW IN ORDER TO USE THE EXAMPLES FROM RANDOM NERD TUTORIALS ***
   FULL INSTRUCTIONS AVAILABLE ON HOW CONFIGURE THE LIBRARY: https://RandomNerdTutorials.com/cyd-lvgl/ or https://RandomNerdTutorials.com/esp32-tft-lvgl/  */
#include <TFT_eSPI.h>

#include <image.h>

#define SCREEN_WIDTH 320
#define SCREEN_HEIGHT 240
#define DRAW_BUF_SIZE (SCREEN_WIDTH * SCREEN_HEIGHT / 10 * (LV_COLOR_DEPTH / 8))
uint32_t draw_buf[DRAW_BUF_SIZE / 4];

// 如果启用了日志记录，它将告知用户库中正在发生的事情
void log_print(lv_log_level_t level, const char * buf) {
  LV_UNUSED(level);
  Serial.println(buf);
  Serial.flush();
}

void draw_image(void) {
  LV_IMAGE_DECLARE(my_image);
  lv_obj_t * img1 = lv_image_create(lv_screen_active());
  lv_image_set_src(img1, &my_image);
  lv_obj_align(img1, LV_ALIGN_CENTER, 0, 0);
}

void setup() {
  String LVGL_Arduino = String("LVGL Library Version: ") + lv_version_major() + "." + lv_version_minor() + "." + lv_version_patch();
  Serial.begin(115200);
  Serial.println(LVGL_Arduino);

  // 启动LVGL
  lv_init();
  // 注册打印函数用于调试
  lv_log_register_print_cb(log_print);

  // 创建显示对象
  lv_display_t * disp;
  // 使用TFT_eSPI库初始化TFT显示屏
  disp = lv_tft_espi_create(SCREEN_WIDTH, SCREEN_HEIGHT, draw_buf, sizeof(draw_buf));

  // 绘制图像的函数
  draw_image();
}

void loop() {
  lv_task_handler();  // 让GUI完成它的工作
  lv_tick_inc(5);     // 告诉LVGL已经过去了多少时间
  delay(5);           // 让这段时间过去
}

```

## 代码是如何工作的？

让我们看看如何在CYD屏幕上显示图像。或者，你可以跳到演示部分。

### 包含库

你需要包括lvgl.h和TFT_eSPI.h库来在屏幕上通信和显示文本。

```c
#include <lvgl.h>
#include <TFT_eSPI.h>
```

包括包含所有绘制图像信息的image.h文件。

```c
#include <image.h>
```

### setup()

在setup()中，包括以下用于调试的行。这些将打印你正在使用的LVGL的版本。你必须使用版本9。

```c
String LVGL_Arduino = String("LVGL Library Version:") + lv_version_major() + "." + lv_version_minor() + "." + lv_version_patch();
Serial.begin(115200);
Serial.println(LVGL_Arduino);
```

#### 初始化LVGL库

通过在setup()中调用lv_init()函数来初始化LVGL库。

```c
// 启动LVGL
lv_init();
```

#### 注册调试函数

注册你之前声明的log_print()函数作为与LVGL调试相关的函数。

```c
// 注册打印函数用于调试
lv_log_register_print_cb(log_print);
```

#### 创建显示对象

要写入显示，你必须首先创建一个显示对象。在你的所有LVGL草图中都需要这样做。以下行将创建一个名为disp的LVGL显示对象，其中包含之前定义的屏幕宽度、屏幕高度和绘图缓冲区。

```c
// 创建一个显示对象
lv_display_t * disp;
// 使用TFT_eSPI库初始化TFT显示屏
disp = lv_tft_espi_create(SCREEN_WIDTH, SCREEN_HEIGHT, draw_buf, sizeof(draw_buf));
```

#### 绘制图像

在setup()中调用draw_image()函数来在显示上绘制图像。然后，一切都通过事件和回调函数工作。代码总是在后台监听事件。当发生某些事情时，它将运行与事件相关的回调函数。你不需要在loop()中检查任何事件。

```c
draw_image();
```

### draw_image()

我们创建了一个名为draw_image()的函数，该函数在setup()中被调用。

```c
void draw_image(void) {
  LV_IMAGE_DECLARE(my_image);
  lv_obj_t * img1 = lv_image_create(lv_screen_active());
  lv_image_set_src(img1, &my_image);
  lv_obj_align(img1, LV_ALIGN_CENTER, 0, 0);
}
```

首先使用以下行声明你的图像（my_image在_image.h文件中声明）。

```c
LV_IMAGE_DECLARE(my_image);
```

然后，使用lv_image_create()函数在当前屏幕上创建一个名为img1的LVGL图像对象。

```c
lv_obj_t * img1 = lv_image_create(lv_screen_active());
```

使用lv_image_set_src()函数设置图像源。将LVGL图像对象和图像源作为参数传递。

```c
lv_image_set_src(img1, &my_image);
```

将图像居中对齐到屏幕。

```c
lv_obj_align(img1, LV_ALIGN_CENTER, 0, 0);
```

### loop()

在loop()中，你可以添加任何其他任务，你需要你的ESP32做，就像在任何常规Arduino草图中一样。在我们的例子中，我们不会向loop()添加任何任务，但为了保持LVGL运行和检测事件，你总是需要将以下行添加到你的loop()中。

```c
void loop() {
  lv_task_handler();   // 让GUI完成它的工作
  lv_tick_inc(5);      // 告诉LVGL已经过去了多少时间
  delay(5);            // 让这段时间过去
}
```

## 演示

你的 _image.h_ 文件应该紧挨着你的 _.ino_ 文件。当你上传代码时， _image.h_ 文件将自动上传到板子上。

准备好这两个文件后，将代码上传到你的板子上。转到**工具** > **板**并选择**ESP32** > **ESP32 Dev Module**。然后，在**工具** > **端口**中选择正确的COM端口。最后，点击上传按钮。

几秒钟后，屏幕将显示如下所示的图像。

![显示图片](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2024/08/ESP32-CYD-board-with-LVGL-Display-Image-on-the-screen.jpg?w=750&quality=100&strip=all&ssl=1)

## 总结

在本教程中，你学习了如何使用LVGL库在Cheap Yellow Display (CYD) 板上显示图像。
