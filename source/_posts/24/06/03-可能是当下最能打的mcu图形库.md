---
title: 最能打的MCU图形库：LVGL
date: 2024-06-03 14:40:29
categories:
tags:
    - diy
---

在讨论图形用户界面（GUI）库时，很多人会想到emWin、TouchGFX以及QT等。这些库虽然功能强大，但它们普遍存在一个共同的问题：对资源的需求较高，不适用于资源有限的微控制器（MCU）。有没有一种既能满足基本GUI需求又能尽可能减少资源占用的GUI库呢？答案是有的，当前最具潜力的选择之一是LVGL（Light and Versatile Graphics Library）。

![智能手表DEMO](https://github.com/kisvegabor/test/raw/master/smartwatch_demo.gif)

<!-- more -->

![仪表盘DEMO](https://camo.githubusercontent.com/7593164c6b55d597309d3397f9cda8072847195a24fd7abc33bf5746ee7c0661/68747470733a2f2f6c76676c2e696f2f6173736574732f696d616765732f6c76676c5f776964676574735f64656d6f2e676966)

## 概述

### 成熟且知名

LVGL 是当前最流行的免费开源嵌入式图形库，能够为任何MCU、MPU和显示器类型创建美观的用户界面。它得到了许多行业领先供应商和项目的支持，包括Arm、STM32、NXP、Espressif、Nuvoton、Arduino、RT-Thread、Zephyr、NuttX、Adafruit等。

### 功能丰富

LVGL 拥有创建现代美观GUI所需的所有功能：30多个内置控件、强大的样式系统、类似网页的布局管理器以及支持多种语言的排版系统。要将LVGL集成到你的平台中，只需要至少32 KB RAM和128 KB Flash、C编译器、帧缓冲区以及至少1/10屏幕大小的渲染缓冲区。

## 特性

### 免费和可移植性

- 一个完全可移植的 C（C++ 兼容）库，没有外部依赖关系。
- 可以编译到任何 MCU 或 MPU，使用任何 RTOS 或者操作系统。
- 支持单色、ePaper、OLED、TFT 显示器或者模拟器。
- [移植指南](https://docs.lvgl.io/master/porting/project.html)
- 该项目使用 MIT 许可证，因此您可以在商业项目中轻松使用它。
- 仅需 32 KB RAM 和 128 KB Flash，一个帧缓冲区，以及至少 1/10 屏幕大小的渲染缓冲区。
- 支持使用可选的操作系统、外部存储器和 GPU。

### 控件、样式、布局等

- 30+ 内置[控件](https://docs.lvgl.io/master/widgets/index.html)：按钮、标签、滑块、图表、键盘、仪表、弧形、表格等等。
- 灵活的[样式系统](https://docs.lvgl.io/master/overview/style.html)支持约 100 个样式属性，可在任何状态下自定义控件的任何部分。
- [Flex 布局](https://docs.lvgl.io/master/layouts/flex.html) 和 [Grid 布局](https://docs.lvgl.io/master/layouts/grid.html)可以响应式自动调整控件的大小和位置。
- 文本支持 UTF-8 编码，支持 CJK、泰语、印地语、阿拉伯语和波斯语书写系统。
- 支持自动换行、字距调整、文本滚动、亚像素渲染、拼音输入法、文本表情符号。
- 渲染引擎支持动画、抗锯齿、不透明度、平滑滚动、阴影、图形变换等。
- 支持鼠标、触摸板、小键盘、键盘、外部按钮、编码器[输入设备](https://docs.lvgl.io/master/porting/indev.html)。
- 支持[多显示器](https://docs.lvgl.io/master/overview/disp.html#multiple-display-support)。

### 绑定和构建支持

- [Microython 绑定](https://blog.lvgl.io/2019-02-20/micropython-bindings)公开 LVGL的API
- [PikaScript 绑定](https://blog.lvgl.io/2022-08-24/pikascript-and-lvgl)在 MCU 上的更轻更简单的 Python 版本
- 未使用自定义生成系统。您可以在构建项目的其他文件时构建 LVGL。
- 支持开箱即用的 Make 和 [CMake](https://docs.lvgl.io/master/get-started/platforms/cmake.html)编译系统。
- 支持在 [PC 上开发](https://docs.lvgl.io/master/get-started/platforms/pc-simulator.html)，并可以在嵌入式硬件上使用相同的 UI 代码。
- 支持使用我们的 [Emscripten 移植](https://github.com/lvgl/lv_web_emscripten)从而将 C 写的 UI 代码转换为 HTML 文件。

### 文档、工具和服务

- 包含 [100 多个简单示例](https://docs.lvgl.io/master/index.html)的详细[文档](https://docs.lvgl.io/)
- [服务](https://lvgl.io/services)如用户界面设计、实施和咨询，使 UI 开发更简单、更快。

## 支持包

LVGL 可用于以下几种：

- [Arduino library](https://docs.lvgl.io/master/integration/framework/arduino.html)
- [PlatformIO package](https://registry.platformio.org/libraries/lvgl/lvgl)
- [Zephyr library](https://docs.lvgl.io/master/integration/os/zephyr.html)
- [ESP-IDF(ESP32) component](https://components.espressif.com/components/lvgl/lvgl)
- [NXP MCUXpresso component](https://www.nxp.com/design/software/embedded-software/lvgl-open-source-graphics-library:LITTLEVGL-OPEN-SOURCE-GRAPHICS-LIBRARY)
- [NuttX library](https://docs.lvgl.io/master/integration/os/nuttx.html)
- [RT-Thread RTOS](https://docs.lvgl.io/master/integration/os/rt-thread.html)
- [CMSIS-Pack](https://arm-software.github.io/CMSIS_6/latest/General/cmsis_pack.html)
- [RIOT OS package](https://doc.riot-os.org/group__pkg__lvgl.html#details)

## 使用LVGL的优势

### 资源占用低

LVGL 是专为资源有限的MCU设计的，只需要最低32 KB的RAM和128 KB的Flash，这使得它特别适合嵌入式系统。

### 高性能

尽管资源占用低，LVGL仍然提供了高性能的图形渲染能力，包括支持动画、抗锯齿、阴影效果等高级特性。

### 高度可定制

LVGL 提供了高度灵活的样式和布局系统，使得用户可以根据需要定制控件的外观和行为。此外，LVGL 的开源性质允许用户根据项目需求进行修改和扩展。

### 强大的社区支持

作为一个开源项目，LVGL 拥有活跃的开发者社区和广泛的用户基础。你可以在官方论坛、GitHub、Discord等平台上获得支持和。

### 跨平台支持

LVGL 支持多种显示器类型和输入设备，并且可以运行在多种MCU、MPU和RTOS上，确保了其广泛的适用性和灵活性。

## 如何开始使用LVGL

### 安装和配置

要开始使用LVGL，你需要先下载和安装库文件。可以从GitHub获取最新版本的LVGL库，并按照文档中的指引进行配置。以下是一个简单的步骤概述：

1. 克隆LVGL库：
   ```bash
   git clone https://github.com/lvgl/lvgl.git
   ```

2. 配置你的开发环境，确保编译器、工具链和显示驱动程序已正确安装。

3. 在你的项目中包含LVGL库文件，并根据需要配置样式、控件和布局。

### 创建一个简单的UI

下面是一个简单的示例代码，展示如何使用LVGL创建一个按钮并响应点击事件：

```c
#include "lvgl/lvgl.h"

int main(void)
{
    lv_init();
    
    /* Your hardware initialization code here */
    
    lv_obj_t *btn = lv_btn_create(lv_scr_act());     // Create a button
    lv_obj_set_pos(btn, 10, 10);                     // Set its position
    lv_obj_set_size(btn, 100, 50);                   // Set its size

    lv_obj_t *label = lv_label_create(btn);          // Add a label to the button
    lv_label_set_text(label, "Button");              // Set the label text

    lv_obj_add_event_cb(btn, my_event_handler, LV_EVENT_CLICKED, NULL);  // Add event handler

    while(1) {
        lv_task_handler();  // Handle LVGL tasks
        usleep(5 * 1000);
    }

    return 0;
}

void my_event_handler(lv_event_t *e)
{
    printf("Button clicked!\n");
}
```

这个示例展示了如何创建一个按钮，并为其添加一个点击事件处理程序。在实际使用中，你可以根据项目需求创建更复杂的UI界面。

LVGL 是一个强大且资源高效的嵌入式图形库，适用于各种MCU和显示设备。其丰富的功能、高度的可定制性和强大的社区支持，使其成为嵌入式开发中的一个理想选择。无论是小型项目还是复杂系统，LVGL 都能帮助你快速创建美观、高性能的用户界面。如果你正在寻找一个既能满足基本GUI需求又能在资源占用上做到最小的解决方案，那么LVGL无疑是一个非常值得考虑的选项。

你会将它用在什么项目上呢？