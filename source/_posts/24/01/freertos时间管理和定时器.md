---
title: freertos时间管理和定时器
date: 2024-01-02 11:17:54
categories:
tags:
    - freertos
    - 嵌入式
---

FreeRTOS 是一个开源的实时操作系统，提供了丰富的时间管理和定时器功能，用于实现任务调度、延时等操作。在本文中，我们将深入探讨 FreeRTOS 的时间管理和定时器相关的内容。

## 时间管理

FreeRTOS 提供了以下几种与时间相关的管理功能：

### 1. `vTaskDelay`函数

`vTaskDelay` 函数用于使任务进入阻塞状态，以实现延时功能。延时的时间以 FreeRTOS 的时钟节拍（tick）为单位。

```c
#include "FreeRTOS.h"
#include "task.h"

void vTaskFunction(void *pvParameters)
{
    // 任务执行的代码

    vTaskDelay(pdMS_TO_TICKS(1000));  // 延时 1000 毫秒
}
```

### 2. `vTaskDelayUntil` 函数

`vTaskDelayUntil` 函数用于设置任务的周期性执行，它在指定的绝对时间执行任务，而不是相对于当前时间的相对延时。

```c
#include "FreeRTOS.h"
#include "task.h"

void vTaskFunction(void *pvParameters)
{
    TickType_t xLastWakeTime = xTaskGetTickCount();

    for (;;)
    {
        // 任务执行的代码

        // 等待下一个周期
        vTaskDelayUntil(&xLastWakeTime, pdMS_TO_TICKS(1000));
    }
}
```

<!-- more -->

### 3. 获取系统时间

`xTaskGetTickCount` 函数用于获取系统自启动以来的时钟节拍数，以及 `xTaskGetTickCountFromISR` 用于中断服务例程中获取时钟节拍数。

```c
#include "FreeRTOS.h"
#include "task.h"

void vTaskFunction(void *pvParameters)
{
    TickType_t currentTickCount = xTaskGetTickCount();
    // 执行任务代码
}
```

`xTaskGetTickCountFromISR` 是 FreeRTOS 提供的一个用于在中断服务例程（ISR，Interrupt Service Routine）中获取系统时钟节拍数的函数。由于在中断上下文中，一些 FreeRTOS API 是不允许使用的，因此需要使用特定的 ISR 版本的函数来执行相应的操作。

函数原型：

```c
TickType_t xTaskGetTickCountFromISR(void);
```

- **返回值：** 当前的系统时钟节拍数（以 `TickType_t` 类型表示）。

### 示例代码

下面是一个简单的示例，演示了如何在中断服务例程中使用 `xTaskGetTickCountFromISR` 函数：

```c
#include "FreeRTOS.h"
#include "task.h"
#include "stdio.h"

// 模拟一个中断服务例程
void vISRFunction(void)
{
    BaseType_t xHigherPriorityTaskWoken = pdFALSE;

    // 获取当前的系统时钟节拍数
    TickType_t currentTickCount = xTaskGetTickCountFromISR();

    // 执行中断服务例程的操作

    // 通知等待的任务发生了事件
    vTaskNotifyGiveFromISR(/*任务句柄*/, &xHigherPriorityTaskWoken);

    // 如果有任务的优先级高于当前运行的任务，需进行任务切换
    portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}

void vTaskFunction(void *pvParameters)
{
    for (;;)
    {
        // 等待中断服务例程通知
        ulTaskNotifyTake(pdTRUE, portMAX_DELAY);

        // 任务执行的代码
        printf("Task is running. Current tick count: %lu\n", xTaskGetTickCount());
    }
}

int main(void)
{
    // 创建任务
    xTaskCreate(vTaskFunction, "Task", 1000, NULL, 1, NULL);

    // 模拟中断服务例程定期触发
    for (;;)
    {
        vISRFunction();

        // 模拟中断的周期
        vTaskDelay(pdMS_TO_TICKS(1000));
    }

    return 0;
}
```

在上述示例中，`vISRFunction` 模拟一个中断服务例程，其中使用了 `xTaskGetTickCountFromISR` 函数来获取当前系统时钟节拍数。随后，通过 `vTaskNotifyGiveFromISR` 函数通知等待的任务发生了事件，并通过 `portYIELD_FROM_ISR` 判断是否需要进行任务切换。

请注意，在实际中断服务例程中，应该谨慎使用 FreeRTOS API，并根据实际情况选择是否需要任务切换。在某些情况下，可能需要在中断服务例程中延迟任务的执行，以避免破坏实时性。

## 定时器

FreeRTOS 提供了定时器作为一种更灵活的时间管理工具，可以用于周期性执行任务或在一段时间后触发事件。

### 1. 创建定时器

使用 `xTimerCreate` 函数创建定时器，并指定回调函数。

```c
#include "FreeRTOS.h"
#include "timers.h"

void vTimerCallback(TimerHandle_t xTimer)
{
    // 定时器回调函数，执行相应操作
}

void vTaskFunction(void *pvParameters)
{
    TimerHandle_t xTimer = xTimerCreate(
        "MyTimer",               // 定时器名称
        pdMS_TO_TICKS(1000),     // 周期为 1000 毫秒
        pdTRUE,                  // 自动重载
        NULL,                    // 定时器ID
        vTimerCallback);         // 回调函数

    if (xTimer != NULL)
    {
        // 启动定时器
        xTimerStart(xTimer, 0);
    }

    // 任务执行的代码
}
```

### 2. 定时器示例

下面是一个完整的示例，演示了如何使用 FreeRTOS 定时器。

```c
#include "FreeRTOS.h"
#include "timers.h"
#include "task.h"

void vTimerCallback(TimerHandle_t xTimer)
{
    // 定时器回调函数，执行相应操作
    printf("Timer expired!\n");
}

void vTaskFunction(void *pvParameters)
{
    TimerHandle_t xTimer = xTimerCreate(
        "MyTimer",               // 定时器名称
        pdMS_TO_TICKS(1000),     // 周期为 1000 毫秒
        pdTRUE,                  // 自动重载
        NULL,                    // 定时器ID
        vTimerCallback);         // 回调函数

    if (xTimer != NULL)
    {
        // 启动定时器
        xTimerStart(xTimer, 0);

        for (;;)
        {
            // 任务执行的代码
        }
    }
}

int main(void)
{
    xTaskCreate(vTaskFunction, "Task", 1000, NULL, 1, NULL);
    vTaskStartScheduler();

    return 0;
}
```

在这个示例中，创建了一个名为 "MyTimer" 的定时器，每隔 1000 毫秒触发一次回调函数 `vTimerCallback`。回调函数中输出 "Timer expired!"。任务函数中启动了定时器，并进入一个无限循环的任务执行代码。

以上是 FreeRTOS 的时间管理和定时器相关内容的简要介绍。通过使用这些功能，可以更加灵活地管理任务的执行时间和周期性任务的触发。在实际项目中，合理利用时间管理和定时器功能将对系统的稳定性和实时性产生积极影响。

## 项目实例

假设我们正在开发一个嵌入式系统，这个系统负责控制智能家居中的温度和湿度监控。我们需要定期采集传感器数据，更新显示，以及根据设定的温度阈值触发相应的控制操作。在这个项目中，时钟和定时器将起到关键作用。

### 1. 系统初始化

首先，在系统初始化阶段，我们需要初始化 FreeRTOS 时钟节拍和创建定时器。

```c
#include "FreeRTOS.h"
#include "task.h"
#include "timers.h"

// 定义任务和定时器句柄
TaskHandle_t xTaskHandle;
TimerHandle_t xTimerHandle;

// 任务函数
void vTemperatureTask(void *pvParameters);

// 定时器回调函数
void vTimerCallback(TimerHandle_t xTimer);

int main(void)
{
    // 创建任务
    xTaskCreate(vTemperatureTask, "TemperatureTask", 1000, NULL, 1, &xTaskHandle);

    // 创建定时器，每隔1秒触发一次回调函数
    xTimerHandle = xTimerCreate("SensorTimer", pdMS_TO_TICKS(1000), pdTRUE, (void *)0, vTimerCallback);

    // 启动定时器
    if (xTimerHandle != NULL)
    {
        xTimerStart(xTimerHandle, 0);
    }

    // 启动任务调度器
    vTaskStartScheduler();

    return 0;
}
```

### 2. 温度任务

温度任务周期性地采集传感器数据，并更新显示。

```c
#include "FreeRTOS.h"
#include "task.h"

void vTemperatureTask(void *pvParameters)
{
    for (;;)
    {
        // 采集传感器数据
        float temperature = readTemperatureSensor();

        // 更新显示
        updateDisplay(temperature);

        // 等待下一个周期
        vTaskDelay(pdMS_TO_TICKS(5000)); // 5秒周期
    }
}
```

### 3. 定时器回调函数

定时器回调函数用于检查温度是否超过设定的阈值，并触发相应的控制操作。

```c
#include "FreeRTOS.h"
#include "timers.h"

void vTimerCallback(TimerHandle_t xTimer)
{
    // 读取当前温度
    float currentTemperature = readTemperatureSensor();

    // 检查是否超过设定的阈值
    if (currentTemperature > TEMPERATURE_THRESHOLD)
    {
        // 执行控制操作，例如打开风扇或空调
        controlTemperature(currentTemperature);
    }
}
```

通过这个示例，我们展示了在一个智能家居温控系统中如何利用 FreeRTOS 的时钟和定时器功能。温度任务定期采集传感器数据，而定时器回调函数则用于实时监测温度并触发相应的控制操作。这种基于时钟和定时器的任务调度方式可以确保系统按照预定的周期执行任务，提高系统的实时性和可靠性。
