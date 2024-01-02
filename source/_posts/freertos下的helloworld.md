---
title: freertos下的helloworld
date: 2024-01-02 11:10:06
categories:
tags:
    - 嵌入式
    - freertos
---

## 什么是实时操作系统（RTOS）

实时操作系统（RTOS）是一种专为实时应用程序设计的操作系统。实时应用程序需要在特定时间内做出预测的响应，因此RTOS专注于提供对时间约束的强调，以确保系统能够满足实时性能要求。

### 1. **实时性能：**

- **硬实时和软实时：** RTOS可以根据实时性能的要求分为硬实时和软实时。硬实时系统对任务的响应时间有极严格的要求，任何未能在规定时间内完成的任务都会被认为是失败。软实时系统也有时间要求，但相对灵活，任务未能在规定时间内完成并不会导致系统失败。

### 2. **任务调度：**

- **实时调度算法：** RTOS使用实时调度算法来确定哪个任务将在处理器上运行。这些算法考虑任务的优先级、截止期限和其他实时要求。
- **时间片轮转：** 在一些情况下，RTOS可能使用时间片轮转调度，确保每个任务都有机会运行，以防止某个任务长时间独占处理器。

### 3. **任务管理：**

- **任务创建与删除：** RTOS允许创建和删除任务。任务可以是独立运行的单元，执行特定的功能。
- **任务同步和通信：** 提供机制使任务能够同步和通信，如信号量、互斥锁、消息队列等。

### 4. **内存管理：**

- **动态内存分配：** 一些RTOS支持动态内存分配，允许在运行时分配和释放内存。
- **静态内存分配：** 为了提高可预测性，一些RTOS可能支持静态内存分配，即在编译时为任务分配内存。

### 5. **中断处理：**

- **中断服务例程（ISR）：** RTOS允许开发人员编写中断服务例程，以处理硬件中断事件。
- **中断优先级：** 中断服务例程可以具有不同的优先级，RTOS确保高优先级的中断可以中断低优先级的中断或任务。

### 6. **实时时钟和定时器：**

- **实时时钟：** 提供对实时时钟的支持，以便任务能够基于实际时间进行调度和同步。
- **定时器：** 允许设置和管理定时器，以在指定时间触发任务或事件。

### 7. **错误处理：**

- **错误检测与处理：** RTOS通常提供错误检测和处理机制，以确保系统在出现错误时能够采取适当的措施。

### 8. **RTOS应用领域：**

- **嵌入式系统：** RTOS广泛应用于嵌入式系统，如汽车控制单元、医疗设备、工业自动化等。
- **通信系统：** 在通信系统中，RTOS用于管理网络设备、路由器和交换机等。

### 9. **示例RTOS：**

- **FreeRTOS：** 一个开源的实时操作系统，广泛用于嵌入式系统。
- **VxWorks：** 用于嵌入式实时系统的商业RTOS。
- **RTOS-32：** 针对Windows平台的实时操作系统。

### 10. **RTOS与通用操作系统的区别：**

- **实时性能：** RTOS关注实时性能，而通用操作系统通常更注重吞吐量和响应时间。
- **内核大小：** RTOS通常具有更小、更精简的内核，以确保快速启动和响应。
- **任务调度：** RTOS使用实时调度算法，以确保任务在规定时间内完成。

实时操作系统在对实时性能有严格要求的应用中发挥着关键作用。通过提供对任务调度、同步、通信和实时时钟的支持，RTOS使得开发者能够设计和实现对时间要求极高的系统。在嵌入式系统和实时控制领域，RTOS成为了不可或缺的工具。

<!-- more -->

## [FreeRTOS](https://www.freertos.org/)

FreeRTOS是一个开源的实时操作系统，广泛用于嵌入式系统。FreeRTOS提供了对任务调度、同步、通信和实时时钟的支持，使得开发者能够设计和实现对时间要求极高的系统。FreeRTOS 通过 MIT 开源许可免费分发，包括一个内核和一组不断丰富的 IoT 库，适用于所有行业领域。FreeRTOS 的构建突出可靠性和易用性。

FreeRTOS 的开发活动已从 SVN 迁移到 GitHub，现在可以直接在FreeRTOS的 [Github](https://github.com/FreeRTOS)页面上找到。 从 GitHub 下载 [FreeRTOS早期版本](https://github.com/FreeRTOS/FreeRTOS/releases)的标准 zip (.zip) 文件或自解压 zip 文件 (.exe) 。解压缩 源代码，同时确保不改动文件夹结构。

FreeRTOS官方下载页有两个版本的下载包，一个是最新版，一个是长期支持（LTS）版。最新版下载包包含最新 [FreeRTOS 内核](https://www.freertos.org/zh-cn-cmn-s/RTOS.html)、 [FreeRTOS-Plus 库](https://www.freertos.org/zh-cn-cmn-s/FreeRTOS-Plus/index.html) 和 [AWS IoT 库](https://www.freertos.org/zh-cn-cmn-s/iot-libraries.html)，以及示例项目。长期支持版下载包则包含FreeRTOS LTS 库，其中包括 FreeRTOS 内核和 IoT 库，但没有示例项目。可以根据自己需要选择下载。

因为手上正好有ESP32-C3小飞控板，所以接下来的playground就以这个小板子为例了。而ESP-IDF中已经集成了FreeRTOS，所以我们可以直接使用ESP-IDF来开发FreeRTOS应用。

## Hello World

配置好ESP-IDF开发环境后，按照以下步骤配置实现本示例项目：

### 步骤 1: 安装 ESP-IDF

确保你已经安装了 ESP-IDF。你可以按照 Espressif 的官方文档 [ESP-IDF 安装指南](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html) 进行安装。

### 步骤 2: 创建新项目

1. 打开终端或命令提示符窗口，切换到你希望创建项目的目录。

2. 运行以下命令来创建一个新的 ESP-IDF 项目：

```powershell
idf.py create-project hello_world
```

这将创建一个包含默认项目结构的新目录。

### 步骤 3: 进入项目目录

```powershell
cd <project-name>
```

### 步骤 4: 配置项目

1. 运行以下命令配置项目：

```powershell
idf.py menuconfig
```

这将打开一个菜单配置界面，你可以在其中配置项目的各种选项，包括串口设置、Wi-Fi 配置、组件选项等。配置完成后，保存并退出。

2. (可选) 如果你想使用 VSCode 或其他 IDE 进行开发，可以运行以下命令生成 IDE 项目文件：

```powershell
idf.py vscode
```

这将生成与 Visual Studio Code 兼容的配置文件。

### 步骤 5: 编译和烧录

在`main`目录下找到`hello_world_main.c`文件，写入以下内容：

```c
#include <stdio.h>
#include <inttypes.h>
#include "sdkconfig.h"
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_chip_info.h"
#include "esp_flash.h"

void app_main(void)
{
    printf("Hello world!\n");

    /* Print chip information */
    esp_chip_info_t chip_info;
    uint32_t flash_size;
    esp_chip_info(&chip_info);
    printf("This is %s chip with %d CPU core(s), %s%s%s%s, ",
           CONFIG_IDF_TARGET,
           chip_info.cores,
           (chip_info.features & CHIP_FEATURE_WIFI_BGN) ? "WiFi/" : "",
           (chip_info.features & CHIP_FEATURE_BT) ? "BT" : "",
           (chip_info.features & CHIP_FEATURE_BLE) ? "BLE" : "",
           (chip_info.features & CHIP_FEATURE_IEEE802154) ? ", 802.15.4 (Zigbee/Thread)" : "");

    unsigned major_rev = chip_info.revision / 100;
    unsigned minor_rev = chip_info.revision % 100;
    printf("silicon revision v%d.%d, ", major_rev, minor_rev);
    if(esp_flash_get_size(NULL, &flash_size) != ESP_OK) {
        printf("Get flash size failed");
        return;
    }

    printf("%" PRIu32 "MB %s flash\n", flash_size / (uint32_t)(1024 * 1024),
           (chip_info.features & CHIP_FEATURE_EMB_FLASH) ? "embedded" : "external");

    printf("Minimum free heap size: %" PRIu32 " bytes\n", esp_get_minimum_free_heap_size());

    for (int i = 10; i >= 0; i--) {
        printf("Restarting in %d seconds...\n", i);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
    printf("Restarting now.\n");
    fflush(stdout);
    esp_restart();
}
```

这段代码将输出"Hello World！"，并打印一些芯片基本信息。

1. 使用以下命令编译项目：

```powershell
idf.py build
```

2. 使用以下命令烧录固件到 ESP32：

```powershell
idf.py -p <PORT> flash
```

其中 `<PORT>` 是你的 ESP32 开发板连接到计算机的端口。你可以使用 `ls /dev/tty*`（在 Linux/macOS）或 `mode` 命令（在 Windows）来查找端口。

### 步骤 6: 监视串口输出

1. 使用以下命令启动监视串口输出：

```powershell
idf.py -p <PORT> monitor
```

这将打开一个窗口，显示 ESP32 的串口输出。你可以看到程序的调试信息和日志。

2. 如果你修改了代码并重新编译，可以使用以下命令重新烧录并启动监视：

```powershell
idf.py -p <PORT> flash monitor
```

运行成功的话，应该会在控制台输出类似以下内容：

```powershell
Hello world!
This is esp32c3 chip with 1 CPU core(s), WiFi/BLE, silicon revision v0.4, 2MB external flash
Minimum free heap size: 330392 bytes
Restarting in 10 seconds...
Restarting in 9 seconds...
Restarting in 8 seconds...
Restarting in 7 seconds...
Restarting in 6 seconds...
Restarting in 5 seconds...
Restarting in 4 seconds...
Restarting in 3 seconds...
Restarting in 2 seconds...
Restarting in 1 seconds...
Restarting in 0 seconds...
Restarting now.
```
