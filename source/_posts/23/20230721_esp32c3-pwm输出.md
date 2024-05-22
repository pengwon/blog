---
title: esp32c3_pwm输出
date: 2023-07-21 11:54:14
categories:
tags:
    - 硬件
    - 嵌入式
    - esp32
---

目前对于遥控双发差速小飞机计划采用如下架构：

![遥控小飞机框图](https://imgs.boringhex.top/blog/img_v2_4f4d8ee0-0492-4165-acb8-0722eb17d04g.png)

<!-- more -->

- ESP32C3做主控，兼具遥控收发和飞行控制
- 锂电池供电，带电量检测
- 双发，720空心杯电机，55mm桨，带电流检测
- MPU6050加速度计和陀螺仪
- 预留4个控制信号输出

马达控制要用到pwm，今天把esp32c3的pwm跑一下。

## 简介

esp32c3中把pwm外设称为“LED PWM控制器”，共有6个通道，可分配到任意GPIO管脚：

- 四个独立定时器，可实现小数分频
- 波形的周期和占空比可配置，占空比精确度可达14位
- PWM输出信号相位可调节
- 可在Light-sleep 模式下工作
- 多种时钟源选择，包括APB总线时钟、外置主晶振时钟
- 支持硬件自动步进式地增加或减少占空比，可用于LED RGB彩色梯度发生器

占空比自动渐变是一个特色功能，用于呼吸灯或其它LED灯效会很方便，在官方文档特意介绍说是LED PWM控制器。

## 使用方法

![LED PWM控制器架构](https://imgs.boringhex.top/blog/20230718170546.png)

4个定时器可独立配置（可配置时钟分频器和计数器最大值），每个定时器内部有一个时基计数器（即基于基准时钟周期计数的计数器）。每个PWM生成器在四个定时器中择一，以该定时器的计数值为基准生成PWM信号。

下图为定时器和PWM生成器的主要功能块。

![定时器和PWM生成器功能块](https://imgs.boringhex.top/blog/20230718170735.png)

LED PWM控制器的每个定时器内部都有一个时基计数器。上图中时基计数器使用的时钟信号称为`ref_pulsex`。所有定时器使用同一个时钟源信号`LEDC_CLKx`，该时钟源信号经分频器分频后产生`ref_pulsex`供计数器使用。

设置 LEDC 通道分三步完成。注意，与 ESP32 不同，ESP32-C3 仅支持设置通道为低速模式。

1. 定时器配置，指定 PWM 信号的频率和占空比分辨率。
2. 通道配置，绑定定时器和输出 PWM 信号的 GPIO。
3. 改变 PWM 信号，输出 PWM 信号来驱动 LED。可通过软件控制或使用硬件渐变功能来改变 LED 的亮度。

另一个可选步骤是可以在渐变终端设置一个中断。

![LED PWM 控制器 API 的关键配置](https://imgs.boringhex.top/blog/20230718171215.png)

首次 LEDC 配置时，最好先配置定时器（调用函数 `ledc_timer_config()`），再配置通道（调用函数 `ledc_channel_config()`）。这样可以确保 IO 脚上的 PWM 信号自有输出开始其频率就是正确的。

### 定时器配置

要设置定时器，可调用函数 `ledc_timer_config()`，并将包括如下配置参数的数据结构 `ledc_timer_config_t` 传递给该函数：

- 速度模式（值必须为 `LEDC_LOW_SPEED_MODE`）
- 定时器索引 `ledc_timer_t`
- PWM 信号频率（Hz）
- PWM 占空比分辨率
- 时钟源 `ledc_clk_cfg_t`

频率和占空比分辨率相互关联。PWM 频率越高，占空比分辨率越低，反之亦然。如果 API 不是用来改变 LED 亮度，而是用于其它目的，这种相互关系可能会很重要。时钟源同样可以限制PWM频率。选择的时钟源频率越高，可以配置的PWM频率上限就越高。

![esp32c3 ledc时钟源特性](https://imgs.boringhex.top/blog/20230718172416.png)

> 备注
> 
> 1. 如果 ESP32-C3 的定时器选用了RC_FAST_CLK作为其时钟源，驱动会通过内部校准来得知这个时钟源的实际频率。这样确保了输出PWM信号频率的精准性。
> 
> 2. ESP32-C3 的所有定时器共用一个时钟源。因此 ESP32-C3 不支持给不同的定时器配置不同的时钟源。

当一个定时器不再被任何通道所需要时，可以通过调用相同的函数 `ledc_timer_config()` 来重置这个定时器。此时，函数入参的配置结构体需要指定：

- `ledc_timer_config_t::speed_mode` 重置定时器的所属速度模式 （`ledc_mode_t`）
- `ledc_timer_config_t::timer_num` 重置定时器的索引 （`ledc_timer_t`）
- `ledc_timer_config_t::deconfigure` 将指定定时器重置必须配置此项为 `true`

### 通道配置

定时器设置好后，请配置所需的通道（`ledc_channel_t` 之一）。配置通道需调用函数 `ledc_channel_config()`。

通道的配置与定时器设置类似，需向通道配置函数传递包括通道配置参数的结构体 `ledc_channel_config_t` 。

此时，通道会按照 `ledc_channel_config_t` 的配置开始运作，并在选定的 GPIO 上生成由定时器设置指定的频率和占空比的 PWM 信号。在通道运作过程中，可以随时通过调用函数 `ledc_stop()` 将其暂停。

### 改变PWM占空比

调用函数 `ledc_set_duty()` 可以设置新的占空比。之后，调用函数 `ledc_update_duty()` 使新配置生效。要查看当前设置的占空比，可使用 `_get_` 函数 `ledc_get_duty()`。

另外一种设置占空比和其他通道参数的方式是调用 通道配置 一节提到的函数 `ledc_channel_config()`。

传递给函数的占空比数值范围取决于选定的 `duty_resolution`，应为 `0` 至 `(2 ** duty_resolution) - 1`。例如，如选定的占空比分辨率为 `10`，则占空比的数值范围为 `0` 至 `1023`。此时分辨率为 ~0.1%。

![常用配置频率及精度](https://imgs.boringhex.top/blog/20230718173701.png)

## 实例

```c
/* LEDC (LED Controller) basic example

   This example code is in the Public Domain (or CC0 licensed, at your option.)

   Unless required by applicable law or agreed to in writing, this
   software is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   CONDITIONS OF ANY KIND, either express or implied.
*/
#include <stdio.h>
#include "driver/ledc.h"
#include "esp_err.h"

#define LEDC_OUTPUT_IO          (5) // Define the output GPIO
#define LEDC_DUTY               (4095) // Set duty to 50%. ((2 ** 13) - 1) * 50% = 4095
#define LEDC_FREQUENCY          (5000) // Frequency in Hertz. Set frequency at 5 kHz

static void example_ledc_init(void)
{
    // Prepare and then apply the LEDC PWM timer configuration
    ledc_timer_config_t ledc_timer = {
        .speed_mode       = LEDC_LOW_SPEED_MODE,
        .timer_num        = LEDC_TIMER_0,
        .duty_resolution  = LEDC_TIMER_13_BIT,
        .freq_hz          = LEDC_FREQUENCY,  // Set output frequency at 5 kHz
        .clk_cfg          = LEDC_AUTO_CLK
    };
    ESP_ERROR_CHECK(ledc_timer_config(&ledc_timer));

    // Prepare and then apply the LEDC PWM channel configuration
    ledc_channel_config_t ledc_channel = {
        .speed_mode     = LEDC_LOW_SPEED_MODE,
        .channel        = LEDC_CHANNEL_0,
        .timer_sel      = LEDC_TIMER_0,
        .intr_type      = LEDC_INTR_DISABLE,
        .gpio_num       = LEDC_OUTPUT_IO,
        .duty           = 0, // Set duty to 0%
        .hpoint         = 0
    };
    ESP_ERROR_CHECK(ledc_channel_config(&ledc_channel));
}

void app_main(void)
{
    // Set the LEDC peripheral configuration
    example_ledc_init();
    // Set duty to 50%
    ESP_ERROR_CHECK(ledc_set_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_0, LEDC_DUTY));
    // Update duty to apply the new value
    ESP_ERROR_CHECK(ledc_update_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_0));
}
```

编译后运行，捕捉如下波形：

![5kHz 50%占空比](https://imgs.boringhex.top/blog/20230718212602.png)

修改下占空比：

```c
ESP_ERROR_CHECK(ledc_set_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_0, LEDC_DUTY-400));
```

编译后运行，捕捉如下波形：

![5kHz 45.1%占空比](https://imgs.boringhex.top/blog/20230718214917.png)
