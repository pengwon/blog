---
title: freertos任务管理
date: 2024-01-02 11:11:31
categories:
tags:
    - 嵌入式
    - freertos
---

## 任务概述

任务是FreeRTOS中最基本的调度单元，它是一段可执行的代码，可以独立运行。FreeRTOS中的任务是基于优先级的抢占式调度，优先级高的任务可以抢占优先级低的任务的CPU资源。任务的创建、删除、挂起、恢复、设置优先级等操作都是通过调用API函数来实现的。

## 任务的创建

任务的创建是通过调用 `xTaskCreate` 函数来实现的，函数原型如下：

```c
BaseType_t xTaskCreate( TaskFunction_t pxTaskCode,
                        const char * const pcName,
                        const configSTACK_DEPTH_TYPE usStackDepth,
                        void * const pvParameters,
                        UBaseType_t uxPriority,
                        TaskHandle_t * const pxCreatedTask );
```

参数说明：

- pxTaskCode：任务函数的入口地址。
- pcName：任务的名字，用于调试。
- usStackDepth：任务栈的大小，单位为字。
- pvParameters：传递给任务函数的参数。 
- uxPriority：任务的优先级。
- pxCreatedTask：任务创建成功后，返回任务句柄。

<!-- more -->

## 任务的删除

任务的删除是通过调用 `vTaskDelete` 函数来实现的，函数原型如下：

```c
void vTaskDelete( TaskHandle_t xTask );
```

参数说明：

- xTask：任务句柄。

## 任务的挂起和恢复

任务的挂起和恢复是通过调用 `vTaskSuspend` 和 `xTaskResume` 函数来实现的，函数原型如下：

```c
void vTaskSuspend( TaskHandle_t xTaskToSuspend );
BaseType_t xTaskResume( TaskHandle_t xTaskToResume );
```

参数说明：

- xTaskToSuspend：挂起任务句柄。
- xTaskToResume：恢复任务句柄。

## 任务的优先级

任务的优先级是通过调用 `vTaskPrioritySet` 和 `xTaskPriorityGet` 函数来实现的，函数原型如下：

```c
void vTaskPrioritySet( TaskHandle_t xTask, UBaseType_t uxNewPriority );
UBaseType_t xTaskPriorityGet( TaskHandle_t xTask, BaseType_t * pxPreviousPriority );
```

参数说明：

- xTask：任务句柄。
- uxNewPriority：新的优先级。
- pxPreviousPriority：保存原来的优先级。

**注意：** 在FreeRTOS中，优先级数值越大，优先级越高，任务的优先级不能超过 `configMAX_PRIORITIES` 宏定义的值。

## 任务的延时

任务的延时是通过调用 `vTaskDelay` 函数来实现的，函数原型如下：

```c
void vTaskDelay( const TickType_t xTicksToDelay );
```

参数说明：

- xTicksToDelay：延时时间，单位为时钟节拍。

## 任务的阻塞

任务的阻塞是通过调用 `vTaskDelayUntil` 函数来实现的，函数原型如下：

```c
void vTaskDelayUntil( TickType_t * const pxPreviousWakeTime, const TickType_t xTimeIncrement );
```

参数说明：

- pxPreviousWakeTime：保存上一次唤醒的时间。
- xTimeIncrement：下一次唤醒的时间增量。

## 任务的阻塞和唤醒

任务的阻塞和唤醒是通过调用 `xTaskNotifyWait` 和 `xTaskNotify` 函数来实现的，函数原型如下：

```c
UBaseType_t xTaskNotifyWait( UBaseType_t uxBitsToClearOnEntry, UBaseType_t uxBitsToClearOnExit, uint32_t * pulNotificationValue, TickType_t xTicksToWait );
BaseType_t xTaskNotify( TaskHandle_t xTaskToNotify, UBaseType_t uxTaskPriority, uint32_t ulValue, eNotifyAction eAction );
```

参数说明：

- uxBitsToClearOnEntry：在任务进入阻塞前，需要清除的任务通知位。
- uxBitsToClearOnExit：在任务退出阻塞前，需要清除的任务通知位。
- pulNotificationValue：保存任务通知值。
- xTicksToWait：阻塞时间。
- xTaskToNotify：需要通知的任务句柄。
- uxTaskPriority：需要通知的任务优先级。
- ulValue：需要通知的任务通知值。

## Playground

### 创建任务

创建2个优先级都是`1`的任务.

```c
#include <freertos/FreeRTOS.h>
#include <freertos/task.h>
#include <stdio.h>

// 任务1的函数
void task1(void *pvParameter) {
    while (1) {
        printf("这是任务1\n");
        vTaskDelay(1000 / portTICK_PERIOD_MS);  // 1秒的延迟
    }
}

// 任务2的函数
void task2(void *pvParameter) {
    while (1) {
        printf("这是任务2\n");
        vTaskDelay(2000 / portTICK_PERIOD_MS);  // 2秒的延迟
    }
}

void app_main() {
    // 创建任务1
    xTaskCreate(task1, "task1", 2048, NULL, 1, NULL);

    // 创建任务2
    xTaskCreate(task2, "task2", 2048, NULL, 1, NULL);
}
```

运行一下，输出：

```powershell
这是任务1
这是任务1
这是任务2
这是任务1
这是任务1
这是任务2
这是任务1
这是任务1
这是任务2
这是任务1
这是任务1
这是任务2
...
```

我们稍加修改，将任务1中的任务延时去掉，看看任务1会不独占CPU资源：

```c
#include <freertos/FreeRTOS.h>
#include <freertos/task.h>
#include <stdio.h>

// 任务1的函数
void task1(void *pvParameter) {
    while (1) {
    }
}

// 任务2的函数
void task2(void *pvParameter) {
    while (1) {
        printf("这是任务2\n");
        vTaskDelay(2000 / portTICK_PERIOD_MS);  // 2秒的延迟
    }
}

void app_main() {
    // 创建任务1
    xTaskCreate(task1, "task1", 2048, NULL, 2, NULL);

    // 创建任务2
    xTaskCreate(task2, "task2", 2048, NULL, 1, NULL);
}
```

运行一下，输出：

```powershell
这是任务2
这是任务2
E (5509) task_wdt: Task watchdog got triggered. The following tasks/users did not reset the watchdog in time:
E (5509) task_wdt:  - IDLE (CPU 0)
E (5509) task_wdt: Tasks currently running:
E (5509) task_wdt: CPU 0: task1
E (5509) task_wdt: Print CPU 0 (current core) registers
Core  0 register dump:
MEPC    : 0x42007356  RA      : 0x40385920  SP      : 0x3fc90a50  GP      : 0x3fc8b000  
0x42007356: task1 at C:/Users/Peter/Desktop/udp_server/main/udp_server.c:7 (discriminator 1)

0x40385920: vPortTaskWrapper at C:/Users/Peter/esp/esp-idf/components/freertos/FreeRTOS-Kernel/portable/riscv/port.c:205

TP      : 0x3fc88554  T0      : 0x00000000  T1      : 0x00000000  T2      : 0x00000000
S0/FP   : 0x00000000  S1      : 0x00000000  A0      : 0x00000000  A1      : 0x00000000
A2      : 0x00000000  A3      : 0x00000000  A4      : 0x00000000  A5      : 0x42007356
0x42007356: task1 at C:/Users/Peter/Desktop/udp_server/main/udp_server.c:7 (discriminator 1)

A6      : 0x00000000  A7      : 0x00000000  S2      : 0x00000000  S3      : 0x00000000
S4      : 0x00000000  S5      : 0x00000000  S6      : 0x00000000  S7      : 0x00000000
S8      : 0x00000000  S9      : 0x00000000  S10     : 0x00000000  S11     : 0x00000000
T3      : 0x00000000  T4      : 0x00000000  T5      : 0x00000000  T6      : 0x00000000
MSTATUS : 0x00000000  MTVEC   : 0x00000000  MCAUSE  : 0x00000000  MTVAL   : 0x00000000
MHARTID : 0x00000000
这是任务2
这是任务2
...
```

可以看到，相同优先级的任务2依然可以正常运行，而任务1则触发了看门狗。两个任务按照时间片轮转的方式共享CPU资源。可以想见，如果任务1的优先级比任务2高，那么任务1就会独占CPU资源，任务2就无法运行了。
