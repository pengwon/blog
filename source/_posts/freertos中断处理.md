---
title: freertos中断处理
date: 2024-01-02 11:21:39
categories:
tags:
    - 嵌入式
    - freertos
---

## FreeRTOS 中断处理的基础概念

在 FreeRTOS 中，中断处理是通过中断服务例程（ISR，Interrupt Service Routine）实现的。中断服务例程是一个在中断发生时由操作系统调用的函数。FreeRTOS 提供了一些用于在中断处理中使用的函数，以确保在中断上下文中正确使用实时操作系统。

## 中断服务例程的注册和处理

FreeRTOS 允许用户在中断服务例程中注册中断处理函数。这可以通过 `xTaskGetSchedulerState` 函数来判断系统的状态，以决定是否需要调用 FreeRTOS 的中断处理函数。

```c
#include "FreeRTOS.h"
#include "task.h"

void vISRHandler(void)
{
    BaseType_t xHigherPriorityTaskWoken = pdFALSE;

    // 中断处理代码

    // 通知等待的任务发生了事件
    vTaskNotifyGiveFromISR(/*任务句柄*/, &xHigherPriorityTaskWoken);

    // 如果有任务的优先级高于当前运行的任务，需进行任务切换
    portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}

int main(void)
{
    // 注册中断服务例程
    registerISRHandler(vISRHandler);

    // 创建任务
    xTaskCreate(vTaskFunction, "Task", 1000, NULL, 1, NULL);

    // 启动调度器
    vTaskStartScheduler();

    return 0;
}
```

<!-- more -->

## 实例：中断在实际项目中的使用

假设我们正在开发一个嵌入式系统，其中一个传感器通过中断方式通知系统有新的数据可用。我们将使用 FreeRTOS 来处理这个中断事件。

```c
#include "FreeRTOS.h"
#include "task.h"
#include "semphr.h"

// 定义任务和信号量句柄
TaskHandle_t xTaskHandle;
SemaphoreHandle_t xSemaphore;

// 中断服务例程
void vSensorISR(void)
{
    // 通知等待的任务发生了事件
    xSemaphoreGiveFromISR(xSemaphore, NULL);
}

// 任务函数
void vTaskFunction(void *pvParameters)
{
    for (;;)
    {
        // 等待中断事件发生
        if (xSemaphoreTake(xSemaphore, portMAX_DELAY) == pdTRUE)
        {
            // 中断事件处理代码
            processInterruptEvent();
        }
    }
}

int main(void)
{
    // 创建信号量
    xSemaphore = xSemaphoreCreateBinary();

    // 注册中断服务例程
    registerISRHandler(vSensorISR);

    // 创建任务
    xTaskCreate(vTaskFunction, "Task", 1000, NULL, 1, &xTaskHandle);

    // 启动调度器
    vTaskStartScheduler();

    return 0;
}
```

在这个示例中，传感器中断触发 `vSensorISR` 中断服务例程，该例程通过释放信号量 `xSemaphore` 通知等待的任务有中断事件发生。任务函数 `vTaskFunction` 通过等待信号量的方式实现对中断事件的处理。这种方式保证了中断处理的实时性，同时避免了在中断服务例程中直接调用 FreeRTOS API。

在下面这个示例中，我们以使用 STM32F103 微控制器为例，通过按键触发中断。我们将创建一个任务来处理按键触发的中断事件，并在按键被按下时通过队列通知任务。

```c
#include "FreeRTOS.h"
#include "task.h"
#include "queue.h"
#include "stm32f10x.h"

// 定义任务句柄和队列句柄
TaskHandle_t xTaskHandle;
QueueHandle_t xQueue;

// 中断服务例程
void EXTI0_IRQHandler(void)
{
    // 清除中断标志位
    EXTI_ClearITPendingBit(EXTI_Line0);

    // 向队列发送中断事件通知
    BaseType_t xHigherPriorityTaskWoken = pdFALSE;
    xQueueSendFromISR(xQueue, NULL, &xHigherPriorityTaskWoken);

    // 如果有任务的优先级高于当前运行的任务，需进行任务切换
    portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}

// 任务函数
void vTaskFunction(void *pvParameters)
{
    for (;;)
    {
        // 等待中断事件发生
        if (xQueueReceive(xQueue, NULL, portMAX_DELAY) == pdTRUE)
        {
            // 中断事件处理代码
            processInterruptEvent();
        }
    }
}

int main(void)
{
    // 初始化 FreeRTOS
    SystemInit();
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_4);
    xQueue = xQueueCreate(5, sizeof(void *));

    // 配置按键触发的中断
    GPIO_InitTypeDef GPIO_InitStructure;
    EXTI_InitTypeDef EXTI_InitStructure;
    NVIC_InitTypeDef NVIC_InitStructure;

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA | RCC_APB2Periph_AFIO, ENABLE);
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
    GPIO_Init(GPIOA, &GPIO_InitStructure);

    GPIO_EXTILineConfig(GPIO_PortSourceGPIOA, GPIO_PinSource0);

    EXTI_InitStructure.EXTI_Line = EXTI_Line0;
    EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;
    EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Falling;
    EXTI_InitStructure.EXTI_LineCmd = ENABLE;
    EXTI_Init(&EXTI_InitStructure);

    NVIC_InitStructure.NVIC_IRQChannel = EXTI0_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = configLIBRARY_LOWEST_INTERRUPT_PRIORITY;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0x00;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init(&NVIC_InitStructure);

    // 创建任务
    xTaskCreate(vTaskFunction, "Task", configMINIMAL_STACK_SIZE, NULL, 1, &xTaskHandle);

    // 启动调度器
    vTaskStartScheduler();

    return 0;
}
```

在这个示例中，我们首先初始化 FreeRTOS 和配置按键触发的中断。按键触发的中断服务例程 `EXTI0_IRQHandler` 中，我们清除中断标志位并向队列发送中断事件通知。任务函数 `vTaskFunction` 中，我们通过等待队列来处理中断事件。

请注意，配置中断服务例程时，我们使用了 `portYIELD_FROM_ISR` 宏来确保在中断服务例程中进行任务切换。这是因为在 FreeRTOS 中，一些函数需要在任务上下文中调用，而不能在中断上下文中调用。因此，通过 `portYIELD_FROM_ISR` 来通知 FreeRTOS 在中断服务例程中进行任务切换。

## ARM Cortex-M 和 RISC-V 处理器的中断处理机制

ARM Cortex-M 处理器采用了基于向量表的中断处理机制。以下是 ARM Cortex-M 中断处理的基本步骤：

1. **中断向量表（Interrupt Vector Table，IVT）**：
   - 中断向量表是一个包含中断处理程序地址的表格，每个中断对应表中的一个入口。
   - 处理器复位后，会加载 IVT 中的第一个地址作为初始栈指针，接着加载第二个地址作为复位中断服务例程的入口地址。
   - 中断号通过硬件自动映射到中断向量表的相应位置。

2. **中断处理过程**：
   - 当一个中断发生时，处理器会根据中断号查找 IVT 中相应位置的中断处理程序的地址。
   - 处理器保存当前上下文，包括寄存器值和状态寄存器等，将控制权转移到中断服务例程。
   - 中断服务例程执行完毕后，处理器会恢复之前保存的上下文，并返回到中断发生前的状态。

3. **NVIC（Nested Vectored Interrupt Controller）**：
   - Cortex-M 处理器通过 NVIC 管理中断优先级。
   - 可以设置每个中断的优先级，并通过 NVIC 控制中断的使能和屏蔽。

RISC-V 处理器的中断机制相对灵活，主要通过外部中断控制器（PLIC）和本地中断控制器（PLIC）来实现。

1. **PLIC（Platform-Level Interrupt Controller）**：
   - PLIC 负责处理来自外设的中断，并为每个外设提供一个中断号。
   - 外设产生中断时，PLIC 根据优先级和中断使能状态决定是否将中断请求传递给处理器。

2. **PLIC 软中断**：
   - RISC-V 中引入了软中断机制，允许软件通过 `ebreak` 指令触发中断。
   - 这种软中断可以用于系统调用和异常处理。

3. **MTVEC 寄存器**：
   - MTVEC 寄存器用于设置中断向量表的起始地址。
   - 可以选择使用直接模式（Direct Mode）或向量模式（Vector Mode）。

4. **MEPC 寄存器**：
   - MEPC 寄存器保存异常或中断发生时的程序计数器值，用于异常处理结束后的恢复。

### 对比

#### 相似之处：

1. **中断向量表**：两者都使用中断向量表，将中断号映射到相应的中断处理程序地址。

2. **中断服务例程**：处理器在中断发生时都会保存当前上下文，执行相应的中断服务例程，然后恢复之前的上下文。

3. **中断优先级**：都支持中断优先级设置，可以为不同中断指定不同的优先级。

#### 不同之处：

1. **中断控制器**：
   - ARM Cortex-M 使用 NVIC 管理中断优先级和中断使能。
   - RISC-V 使用 PLIC 和 CLIC 来分别处理平台级和核心级的中断，提供更灵活的中断控制。

2. **软中断**：
   - RISC-V 引入了软中断机制，允许软件通过 `ebreak` 指令触发中断，这在系统调用和异常处理中更加灵活。

3. **中断向量表的模式**：
   - ARM Cortex-M 提供了两种模式，Main Table 和 Vector Table Relocation，用于不同的应用场景。
   - RISC-V 提供了直接模式（Direct Mode）和向量模式（Vector Mode）。

4. **异常处理寄存器**：
   - RISC-V 使用 MEPC 寄存器保存异常或中断发生时的程序计数器值，而 ARM Cortex-M 使用 PSP 或 MSP 寄存器来保存栈指针。

总体而言，两者都提供了有效的中断处理机制，选择取决于具体应用场景和需求。 ARM Cortex-M 更加标准化，适用于广泛的嵌入式系统。 RISC-V 的中断机制相对更加灵活，适用于需要高度可配置性和自定义性的场景。
