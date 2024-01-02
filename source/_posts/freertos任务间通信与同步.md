---
title: freertos任务间通信与同步
date: 2024-01-02 11:15:40
categories:
tags:
    - 嵌入式
    - freertos
---

在嵌入式系统中，任务管理是一个重要的部分，它涉及到任务之间的通信和同步，信号量，队列，互斥锁和事件标志组等概念。本文将以FreeRTOS为例，详细讲解这些内容。

## 1. 任务间通信与同步概述

在FreeRTOS中，任务是由一个或多个函数组成的独立的执行流，它们可以独立的运行和调度。任务之间的通信和同步是任务管理的核心内容之一。任务间的通信是指一个任务向另一个任务传递信息，而同步则是指多个任务按照一定的顺序执行。FreeRTOS提供了多种任务间通信和同步的机制，包括信号量，队列，互斥锁和事件标志组等。

## 2. 信号量的使用与实例

信号量（Semaphore）是FreeRTOS中一种常用的同步机制，主要用于任务间和中断服务例程（ISR）间的通信。它们被用来保护共享资源，使得只有一个任务或者中断服务例程可以访问共享资源，避免了资源冲突的问题。

FreeRTOS中的信号量主要有两种类型：计数信号量和二值信号量。

1. 计数信号量（Counting Semaphore）：是一种可以持有多个“计数”或者“票”的信号量。例如，如果你有一些共享资源，每个资源都需要独立的访问控制，你就可以使用一个初始计数等于资源数量的计数信号量。当一个任务需要访问一个资源时，它会尝试“获取”一个信号量。如果信号量计数大于0，那么信号量计数减1，任务继续执行。如果计数为0，那么任务就会阻塞，直到信号量计数大于0。当任务不再需要访问资源时，它应该“释放”信号量，信号量计数加1。

2. 二值信号量（Binary Semaphore）：是一种只有两个值（`0`和`1`）的特殊信号量。它通常被用作任务之间或者任务与中断服务例程之间的同步机制。当信号量的值为`1`时，任务可以获取信号量并继续执行。当信号量的值为`0`时，任务尝试获取信号量会被阻塞，直到信号量的值变为`1`。二值信号量也可以被用作互斥量（`Mutex`），用于保护共享资源的访问。

在FreeRTOS中，信号量的操作主要有创建（`xSemaphoreCreateBinary`, `xSemaphoreCreateCounting`等函数）、获取（`xSemaphoreTake`函数）和释放（`xSemaphoreGive`函数）。在中断服务例程中，获取和释放信号量的函数有所不同，分别为`xSemaphoreTakeFromISR`和`xSemaphoreGiveFromISR`。

<!-- more -->

### 示例

#### 计数信号量

假设有两个任务TaskA和TaskB，TaskA负责发送数据，TaskB负责接收数据。我们使用计数信号量来实现TaskA和TaskB之间的同步。

首先，在FreeRTOS中创建一个计数信号量：

```c
SemaphoreHandle_t xSemaphore;
xSemaphore = xSemaphoreCreateCounting(10, 0); // 创建一个计数信号量，初始计数值为0，最大计数值为10
```

接下来，在TaskA中发送数据时获取计数信号量：

```c
void TaskA(void *pvParameters) {
    while (1) {
        // 发送数据
        ...
        
        // 获取计数信号量，如果计数值为0，则任务被阻塞
        xSemaphoreTake(xSemaphore, portMAX_DELAY);
    }
}
```

在TaskB中接收数据时释放计数信号量：

```c
void TaskB(void *pvParameters) {
    while (1) {
        // 接收数据
        ...
        
        // 释放计数信号量，增加计数值
        xSemaphoreGive(xSemaphore);
    }
}
```

在上述代码中，当TaskA发送数据时，它会获取计数信号量，如果计数值为0，则TaskA会被阻塞，直到TaskB接收数据并释放计数信号量为止。当TaskB释放计数信号量后，计数值增加，TaskA可以继续执行发送数据的操作。

这样，使用计数信号量可以实现TaskA和TaskB之间的同步，保证TaskA在TaskB完成接收数据后才进行发送数据的操作，避免了数据的丢失和冲突。

#### 二值信号量

二值信号量（Binary Semaphore）是一种用于任务间同步和互斥的机制。它只有两个状态，可以是空闲或者被占用，类似于互斥锁。

下面是一个使用二值信号量的示例：

```c
#include "FreeRTOS.h"
#include "task.h"
#include "semphr.h"

// 创建一个二值信号量
SemaphoreHandle_t binarySemaphore;

// 任务1
void task1(void *pvParameters)
{
    while (1)
    {
        // 等待二值信号量
        xSemaphoreTake(binarySemaphore, portMAX_DELAY);

        // 执行任务1的操作
        // ...

        // 释放二值信号量
        xSemaphoreGive(binarySemaphore);

        // 延时一段时间
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}

// 任务2
void task2(void *pvParameters)
{
    while (1)
    {
        // 等待二值信号量
        xSemaphoreTake(binarySemaphore, portMAX_DELAY);

        // 执行任务2的操作
        // ...

        // 释放二值信号量
        xSemaphoreGive(binarySemaphore);

        // 延时一段时间
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}

int main()
{
    // 创建二值信号量
    binarySemaphore = xSemaphoreCreateBinary();

    // 创建任务1
    xTaskCreate(task1, "Task 1", configMINIMAL_STACK_SIZE, NULL, tskIDLE_PRIORITY + 1, NULL);

    // 创建任务2
    xTaskCreate(task2, "Task 2", configMINIMAL_STACK_SIZE, NULL, tskIDLE_PRIORITY + 1, NULL);

    // 启动任务调度器
    vTaskStartScheduler();

    return 0;
}
```

在上面的示例中，首先创建了一个二值信号量`binarySemaphore`，然后创建了两个任务`task1`和`task2`。这两个任务都会在一个无限循环中执行，首先等待二值信号量，然后执行自己的操作，最后释放二值信号量。

当一个任务等待二值信号量时，如果二值信号量的状态是空闲（未被占用），则任务可以获取到该信号量，继续执行自己的操作。如果二值信号量的状态是被占用（已经有任务获取到了信号量），则任务会一直等待，直到信号量被释放。

需要注意的是，当一个任务获取到二值信号量后，其他任务将无法获取到该信号量，直到该任务释放信号量。这就保证了任务之间的互斥性。

通过使用二值信号量，可以实现任务间的同步和互斥操作，确保多个任务按照特定的顺序执行或者避免资源的竞争。


## 3. 队列的使用与实例

队列是一种数据结构，它允许任务按照先进先出（FIFO）的原则将数据项插入队列和从队列中获取数据项。在FreeRTOS中，可以使用xQueueCreate()函数来创建一个队列。

示例：

```c
QueueHandle_t xQueue;

void vProducerTask(void  pvParameters)
{
    int32_t lValueToSend;
    BaseType_t xStatus;

    lValueToSend = (int32_t) pvParameters;

    for(;;)
    {
        xStatus = xQueueSendToBack(xQueue, &lValueToSend, 0);
        if(xStatus != pdPASS)
        {
            printf("Could not send to the queue.\n");
        }
    }
}
```

在这个例子中，vProducerTask任务将数据项发送到队列的尾部。如果发送成功，xQueueSendToBack()函数将返回pdPASS，否则，将返回一个错误代码。

## 4. 互斥锁的使用与实例

互斥锁是一种用于保护共享资源的机制。当一个任务需要使用一个共享资源时，它必须首先获取互斥锁。如果互斥锁已经被另一个任务获取，那么这个任务就需要等待，直到互斥锁被释放。在FreeRTOS中，可以使用xSemaphoreCreateMutex()函数来创建一个互斥锁。

示例：

```c
SemaphoreHandle_t xMutex;

void vTask(void  pvParameters)
{
    for(;;)
    {
        if(xSemaphoreTake(xMutex, (TickType_t)10) == pdTRUE)
        {
            // The mutex was successfully taken, so the shared resource can be accessed.
            printf("Task: Mutex taken!\n");

            // ...
            // Access the shared resource.
            // ...

            // Release the mutex.
            xSemaphoreGive(xMutex);
        }
        else
        {
            // The mutex could not be taken.
            printf("Task: Mutex not taken!\n");
        }
    }
}
```

在这个例子中，vTask任务尝试获取一个互斥锁。如果成功，它将访问一个共享资源，然后释放互斥锁。

## 5. 事件标志组的使用与实例

事件标志组是一种用于表示一组事件发生状态的数据结构。每一个事件都有一个对应的标志，当事件发生时，标志被设置，当事件被处理时，标志被清除。在FreeRTOS中，可以使用xEventGroupCreate()函数来创建一个事件标志组。

示例：

```c
EventGroupHandle_t xEventGroup;

void vTask(void  pvParameters)
{
    EventBits_t uxBits;

    for(;;)
    {
        uxBits = xEventGroupWaitBits(
            xEventGroup,   // The event group being tested.
            BIT_0 | BIT_4, // The bits within the event group to wait for.
            pdTRUE,        // BIT_0 & BIT_4 should be cleared before returning.
            pdFALSE,       // Don't wait for both bits, either bit will do.
            portMAX_DELAY  // Wait a maximum for either bit to be set.
        );

        if((uxBits & (BIT_0 | BIT_4)) == (BIT_0 | BIT_4))
        {
            // Both bits were set.
            printf("Task: Both bits were set!\n");
        }
        else if((uxBits & BIT_0) != 0)
        {
            // Bit 0 was set.
            printf("Task: Bit 0 was set!\n");
        }
        else if((uxBits & BIT_4) != 0)
        {
            // Bit 4 was set.
            printf("Task: Bit 4 was set!\n");
        }
        else
        {
            // Neither bit was set.
            printf("Task: Neither bit was set!\n");
        }
    }
}
```

在这个例子中，vTask任务等待事件标志组中的任何一个事件发生。如果两个事件都发生，它将打印出"Both bits were set!"，如果只有一个事件发生，它将打印出对应的消息。

## 6. 信号量 vs 互斥锁

信号量（Semaphore）和互斥锁（Mutex）都是多任务环境下保护共享资源的一种方法，它们之间存在一些区别：

1. 互斥锁是一种所有权的概念，即一个任务获取了互斥锁后，只有它自己可以释放这个互斥锁，其他任务不能释放。而信号量没有所有权的概念，任何任务都可以释放信号量。

2. 在FreeRTOS中，互斥锁有优先级反转的解决机制，当一个低优先级的任务获取了互斥锁，而高优先级的任务需要这个互斥锁时，低优先级的任务的优先级会被提升，以减少优先级反转的问题。而信号量没有这个机制。

3. 互斥锁通常用于保护共享资源，即在同一时间只能有一个任务访问某个资源。信号量则更多是用于任务同步，它可以被用来唤醒一个或多个等待的任务。

4. 在FreeRTOS中，信号量可以有计数的概念，即可以被“给”多次，每次“给”都会增加一个计数，而互斥锁没有这个概念，它只有锁定和解锁两种状态。

5. 信号量可以被用作二元信号量（即只有两种状态，`0`和`1`，类似互斥锁），而互斥锁不能被用作计数信号量。

## 总结

在FreeRTOS中，任务间的通信和同步是一个重要的部分，它涉及到信号量，队列，互斥锁和事件标志组等概念。通过理解和掌握这些概念，可以有效地管理和调度任务，提高系统的效率和稳定性。
