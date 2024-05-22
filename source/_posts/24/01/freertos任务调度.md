---
title: freertos任务调度
date: 2024-01-02 11:14:21
categories:
tags:
    - 嵌入式
    - freertos
---

## 引言

FreeRTOS是一个流行的小型开源实时操作系统，广泛应用于嵌入式系统领域。本文旨在详细介绍FreeRTOS的任务调度机制，我们将探讨其调度算法的原理，分析调度器的源码，并通过图表帮助理解调度过程。

## FreeRTOS基础

### 实时操作系统简介

实时操作系统（RTOS）是为了能在规定的时间内完成特定任务的操作系统。与通用操作系统不同，实时性是其最重要的特征。

### FreeRTOS的特点

- 轻量级：占用资源少，适合资源受限的嵌入式系统。
- 可裁剪：根据需要启用或禁用功能。
- 多任务支持：支持抢占式和协作式多任务处理。
- 优先级调度：任务根据优先级进行调度。

<!-- more -->

## FreeRTOS任务和调度

FreeRTOS 是一个开源的实时操作系统，它支持多种调度策略，包括协作式（cooperative）和抢占式（preemptive）调度。

### 协作式内核（Cooperative Kernel）：

在协作式调度中，一旦一个任务开始执行，它将继续运行，直到它自己放弃CPU控制权为止。这通常发生在任务主动调用task delay（等待时间片到期）、task yield（放弃剩余时间片）、或者进入阻塞状态（等待事件或资源）时。协作式调度简化了任务间共享资源的管理，因为开发者可以确保在没有明确放弃CPU控制权的情况下，任务不会被中断。但这种模式要求每个任务都必须定期放弃CPU控制权，否则可能导致系统响应性能下降。

### 抢占式内核（Preemptive Kernel）：

抢占式调度允许操作系统根据优先级来决定哪个任务应当获得CPU控制权。当一个高优先级任务变为就绪状态时（例如，它正在等待的事件发生了），系统会中断当前运行的较低优先级任务，把CPU控制权交给高优先级任务。这确保了对紧急任务的快速响应。在这种模式下，任务不需要显式放弃CPU控制权，因为它们可以随时被更高优先级的任务抢占。这种调度方式要求开发者更加注意同步和互斥问题，因为任务可能在任何时候被中断。

FreeRTOS 允许开发者根据应用程序的需求选择最合适的调度策略。在某些应用中，两种策略也可以混合使用。例如，可以设置系统大部分时间运行在协作式调度模式下，但是当特定的中断发生时，中断服务程序可以触发一个更高优先级的任务，从而实现抢占式调度的效果。

选择哪种内核类型通常取决于应用程序对实时性的要求、系统复杂程度、以及开发者对同步机制控制的偏好。协作式内核可能更适合简单的应用程序，或者那些对实时响应要求不是非常严格的场景，而抢占式内核更适合复杂、需要快速响应的实时应用。

### 任务状态

在FreeRTOS中，任务可以处于以下几种状态：

- 就绪（Ready）：任务准备好执行，等待CPU资源。
- 运行（Running）：任务正在执行。
- 阻塞（Blocked）：任务等待某个事件，如延时或外部事件。
- 挂起（Suspended）：任务被挂起，不参与调度。
- 终止（Deleted）：任务已被删除，释放其资源。

### 调度算法

在协作式调度（或称为非抢占式调度）模式下，任务切换不是由系统定时器中断触发的，而是由任务主动触发。这意味着正在运行的任务必须显式地放弃CPU使用权，以便其他任务可以获得执行的机会。如果一个任务不放弃CPU，那么其他任务就无法运行。

我们重点关注基于优先级的抢占式调度算法。具体表现为：

- 抢占式：更高优先级的任务可以抢占正在执行的低优先级任务。
- 时间片轮转：具有相同优先级的任务采用时间片轮转的方式协作运行。

### 任务优先级

FreeRTOS允许设置任务优先级，在标准的FreeRTOS中，任务优先级**数值越小优先级越低**。

## FreeRTOS调度器的工作原理

调度器的工作可以分为三个主要部分：

1. 选择最高优先级的就绪任务
2. 上下文切换
3. 任务切换后的执行

### 选择最高优先级的就绪任务

调度器使用就绪列表（一个或多个就绪优先级数组）来追踪所有就绪状态的任务。列表索引对应任务的优先级。

```c
static List_t pxReadyTasksLists[ configMAX_PRIORITIES ];
```

调度器选取最高优先级的非空列表中的第一个任务作为下一个要运行的任务。

在FreeRTOS中，`List_t`是一个结构体，用于管理一个或一组列表。每个列表可以包含链接在双端链表中的项。这些项通常用于管理准备运行、等待某个事件或处于阻塞或挂起状态的任务。

`List_t`结构体在FreeRTOS源代码中定义，更具体地说是在`list.h`文件中。它的确切定义可能会根据您使用的FreeRTOS版本略有不同，但通常看起来像这样：

```c
typedef struct xLIST {
    UBaseType_t uxNumberOfItems;
    ListItem_t  pxIndex;
    MiniListItem_t xListEnd;
} List_t;
```

以下是`List_t`结构体成员的简要说明：

- `uxNumberOfItems`：这是列表中当前的项或元素的数量。
- `pxIndex`：这是用于遍历列表的指针。它通常指向列表中的一个项。
- `xListEnd`：这是一个特殊的列表项，用于标记列表的末尾。它充当哨兵，所以当遍历列表时，遇到这个项就知道已经到达了末尾。这个项实际上不保存任何用户数据，但用于简化列表操作。

`ListItem_t`和`MiniListItem_t`结构体代表列表中的项，并且也会在`list.h`文件中定义。它们可能看起来像这样：

```c
typedef struct xLIST_ITEM {
    TickType_t xItemValue; / 用于按数值顺序对列表项进行排序。 */
    struct xLIST_ITEM pxNext;
    struct xLIST_ITEM pxPrevious;
    void  pvOwner; / 指向包含列表项的对象（例如TCB）的指针。 /
    void  pvContainer; / 指向包含此列表项的列表的指针（如果有的话）。 /
} ListItem_t;

typedef struct xMINI_LIST_ITEM {
    TickType_t xItemValue;
    struct xLIST_ITEM *pxNext;
    struct xLIST_ITEM *pxPrevious;
} MiniListItem_t;
```
`ListItem_t`结构体用于存储在列表中的所有项，而`MiniListItem_t`是一个简化版本，通常用于列表末尾项。

在`pxReadyTasksLists`数组的上下文中：
- `configMAX_PRIORITIES`：这是在`FreeRTOSConfig.h`中定义的宏，代表FreeRTOS调度器将支持的最大优先级数量。
- `pxReadyTasksLists`：这是一个`List_t`结构体数组，每个元素对应于一个准备运行并且具有相同优先级的任务列表。数组的索引代表优先级，最高优先级在最低索引（0）处。

每个优先级都有自己的任务列表，当调度器选择下一个要运行的任务时，通常从最高优先级的列表开始，并从该列表中选择第一个准备运行的任务。如果该列表为空，则调度器将继续检查下一个优先级的列表，直到找到一个非空列表。如果所有列表都为空，则调度器将选择空闲任务（idle task）。

### 上下文切换

当需要从一个任务切换到另一个任务时，FreeRTOS会保存当前任务的上下文（寄存器等），并恢复下一个任务的上下文。

```c
void vPortYieldFromISR( /.../ ) {
    / Save context of current task. /
    /.../
    
    / Restore context of the next task. /
    /.../
}
```

在FreeRTOS中，上下文切换是指从一个任务切换到另一个任务的过程。这个过程中，操作系统需要保存当前任务的状态，并且恢复下一个要运行的任务的状态。状态信息通常包括CPU的寄存器、程序计数器、堆栈指针等。

上下文切换通常发生在以下几种情况下：

1. 当前任务的时间片用完。
2. 当前任务进入等待状态，例如等待信号量、消息或者延时。
3. 高优先级任务变得就绪，导致抢占。

上下文切换的关键代码取决于特定的处理器架构。下面结合伪代码说明上下文切换的过程：

1. 中断或系统调用：上下文切换通常由中断服务程序（ISR）中的系统调用来触发，例如`vTaskSwitchContext()`函数。在中断发生时，处理器自动保存了一部分上下文，比如程序计数器（PC）、状态寄存器等。

2. 保存寄存器：接下来，FreeRTOS的中断服务例程会保存剩余的寄存器到当前任务的堆栈中。这通常涉及到将通用寄存器（R0-R12）、链接寄存器（LR）、程序计数器（PC）和处理器状态寄存器（xPSR）压栈。

```c
// 示意伪代码
void vPortPendSVHandler(void)
{
    // 保存上下文
    __asm volatile(
        "mrs r0, psp\n\t"
        "stmdb r0!, {r4-r11}\n\t"
        "msr psp, r0\n\t"
        ...
    );

    // 切换任务堆栈指针
    __asm volatile(
        "mov r0, %0\n\t"
        "msr psp, r0\n\t"
        :
        : "r" (pxCurrentTCB->pxTopOfStack)
    );
    
    // 恢复上下文
    __asm volatile(
        "mrs r0, psp\n\t"
        "ldmia r0!, {r4-r11}\n\t"
        "msr psp, r0\n\t"
        ...
    );

    // 退出中断
    __asm volatile(
        "bx lr\n\t"
    );
}
```

3. 选择新任务：调度器会根据任务优先级和其他调度策略来选择下一个要运行的任务。

4. 恢复寄存器：中断服务程序将选择的新任务的状态（即保存在其堆栈中的寄存器值）恢复到对应的寄存器中。

5. 退出中断：当所有的寄存器都恢复后，中断服务程序执行退出中断的指令，新任务开始执行。

上面的代码只是一个示意性的伪代码，具体的实现会根据处理器的不同而有所差异。在实际的FreeRTOS中，`vPortPendSVHandler()`会更复杂，涉及到具体的汇编指令和FreeRTOS的内部数据结构。

在不同的处理器架构中，上下文切换的实现会有所不同。但是，它们的基本原理是一致的：保存当前任务的状态，选择下一个任务，然后恢复下一个任务的状态，以便继续执行。

在ESP-IDF中，上下文切换是由FreeRTOS处理的，具体到ESP32-C3这类基于RISC-V架构的处理器，上下文切换的实现在`portasm.S`文件中。

由于ESP-IDF的代码可能变动，以下是基于目前版本（截至知识截止点2023年）的上下文切换关键代码的分析。请确保检查最新的ESP-IDF代码库以获取最新的代码和信息。

在RISC-V架构中，上下文切换涉及到保存当前任务的状态（寄存器等）以及恢复下一个任务的状态。这通常发生在中断服务程序（Interrupt Service Routine, ISR）中，特别是在定时器中断中，这是FreeRTOS的心跳。

以下是上下文切换的关键代码的简要解析：

1. 保存当前任务的上下文：

   当中断发生时，当前任务的上下文需要被保存。这包括通用寄存器和特殊寄存器的值。在`portasm.S`文件中的`_frxt_int_enter`标签附近可以找到相关代码。

   ```assembly
   .global _frxt_int_enter
   _frxt_int_enter:
       addi    sp, sp, -portCONTEXT_SIZE
       sw      ra, portRA_OFFSET(sp)
       sw      t0, portT0_OFFSET(sp)
       ...
       sw      a0, portA0_OFFSET(sp)
       sw      a1, portA1_OFFSET(sp)
       ...
       csrr    t0, mstatus
       sw      t0, portMSTATUS_OFFSET(sp)
       csrr    t0, mepc
       sw      t0, portMEPC_OFFSET(sp)
   ```

   上述代码中，`addi sp, sp, -portCONTEXT_SIZE`是为保存上下文预留空间，`sw`指令用来将寄存器的值保存到栈上。

2. 切换任务指针：

   任务的上下文被保存后，FreeRTOS将切换当前任务指针。这通常在`xTaskSwitchContext`函数中完成，该函数会选择下一个要运行的任务。

3. 恢复新任务的上下文：

   新任务的上下文需要从其栈中恢复，这在`_frxt_end_task_switch`标签附近的代码中完成。

   ```assembly
   .global _frxt_end_task_switch
   _frxt_end_task_switch:
       lw      t0, portMSTATUS_OFFSET(sp)
       csrw    mstatus, t0
       lw      t0, portMEPC_OFFSET(sp)
       csrw    mepc, t0
       ...
       lw      a0, portA0_OFFSET(sp)
       lw      a1, portA1_OFFSET(sp)
       ...
       lw      ra, portRA_OFFSET(sp)
       addi    sp, sp, portCONTEXT_SIZE
       mret
   ```

   上述代码中，`lw`指令用来从栈上恢复寄存器的值，`csrw`指令恢复特殊寄存器（如`mstatus`和`mepc`），`mret`指令退出中断服务程序回到新任务的执行流。

4. 触发上下文切换：

   在RISC-V架构中，上下文切换通常由定时器中断触发，因此，会在相应的中断处理函数中调用`_frxt_int_enter`和`_frxt_end_task_switch`。

   在ESP-IDF中，上下文切换通常由定时器中断引起，但也可以由其他中断或者系统调用引起。具体的触发点可能会根据FreeRTOS的配置或者硬件细节有所不同。

上述代码是基于汇编语言的，因为上下文切换需要精确控制CPU寄存器和栈的操作。由于汇编语法和指令集是与具体的处理器架构密切相关的，开发者需要对RISC-V汇编有一定的了解才能准确理解上下文切换的过程。

### 任务切换后的执行

新的任务被调度器选中并恢复上下文后，它就会开始执行。

FreeRTOS的调度算法是实时操作系统的核心，它确保系统的实时性和稳定性。通过了解和分析这一过程，我们可以更好地设计和优化我们的嵌入式应用程序。
