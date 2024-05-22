---
title: esp32 udp数据传输
date: 2023-09-25 10:35:37
categories:
tags:
    - esp32
    - 嵌入式
    - diy
---

遥控小飞机的电池舱已经画好发去打样，不过座舱还没搞定，先弄弄遥控和数据传输。

<iframe src="//player.bilibili.com/player.html?bvid=BV148411R7fS&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

本文主要介绍ESP32C3的UDP数据传输，ESP32C3作UDP server，工作在ap模式。

<!-- more -->

先上代码：

```c
#include <string.h>
#include <sys/param.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "esp_wifi.h"
#include "esp_event.h"
#include "esp_log.h"
#include "nvs_flash.h"
#include "esp_netif.h"
#include "protocol_examples_common.h"

#include "lwip/err.h"
#include "lwip/sockets.h"
#include "lwip/sys.h"
#include <lwip/netdb.h>

#define WIFI_SSID "Your_AP_SSID"
#define WIFI_PASS "Your_AP_Password"

#define UDP_PORT 12345

static const char *TAG = "example";

void udp_server_task(void *pvParameters) {
    struct sockaddr_in server_addr, client_addr;
    int sock = socket(AF_INET, SOCK_DGRAM, IPPROTO_IP);

    if (sock < 0) {
        ESP_LOGE(TAG, "Unable to create socket: errno %d", errno);
        vTaskDelete(NULL);
        return;
    }

    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(UDP_PORT);
    server_addr.sin_addr.s_addr = htonl(INADDR_ANY);

    if (bind(sock, (struct sockaddr *)&server_addr, sizeof(server_addr)) < 0) {
        ESP_LOGE(TAG, "Socket unable to bind: errno %d", errno);
        vTaskDelete(NULL);
        return;
    }

    ESP_LOGI(TAG, "UDP server listening");

    while (1) {
        char rx_buffer[128];
        int len = sizeof(client_addr);
        int rx_len = recvfrom(sock, rx_buffer, sizeof(rx_buffer) - 1, 0, (struct sockaddr *)&client_addr, &len);

        if (rx_len <= 0) {
            ESP_LOGE(TAG, "recvfrom failed: errno %d", errno);
            break;
        } else {
            rx_buffer[rx_len] = '\0';
            ESP_LOGI(TAG, "Received %d bytes from %s:%d", rx_len, inet_ntoa(client_addr.sin_addr), ntohs(client_addr.sin_port));
            ESP_LOGI(TAG, "%s", rx_buffer);

            // Respond to the client
            sendto(sock, rx_buffer, rx_len, 0, (struct sockaddr *)&client_addr, sizeof(client_addr));
        }
    }

    if (sock != -1) {
        ESP_LOGE(TAG, "Shutting down socket and restarting...");
        shutdown(sock, 0);
        close(sock);
    }

    vTaskDelete(NULL);
}

void app_main() {
    nvs_flash_init();

    // Initialize TCP/IP stack
    ESP_ERROR_CHECK(esp_netif_init());
    ESP_ERROR_CHECK(esp_event_loop_create_default());

    // Create default event loop and initialize WiFi
    esp_netif_create_default_wifi_ap();
    wifi_init_config_t cfg = WIFI_INIT_CONFIG_DEFAULT();
    ESP_ERROR_CHECK(esp_wifi_init(&cfg));
    wifi_config_t wifi_config = {
        .ap = {
            .ssid = WIFI_SSID,
            .ssid_len = strlen(WIFI_SSID),
            .password = WIFI_PASS,
            .max_connection = 4,
            .authmode = WIFI_AUTH_WPA_WPA2_PSK
        },
    };
    ESP_ERROR_CHECK(esp_wifi_set_mode(WIFI_MODE_AP));
    ESP_ERROR_CHECK(esp_wifi_set_config(WIFI_IF_AP, &wifi_config));
    ESP_ERROR_CHECK(esp_wifi_start());

    xTaskCreate(udp_server_task, "udp_server_task", 4096, NULL, 5, NULL);
}
```

将这段代码编译后烧写到ESP32C3模块中，运行，可以从控制台看到如下输出：

```powershell
--- WARNING: GDB cannot open serial ports accessed as COMx
--- Using \\.\COM5 instead...
--- idf_monitor on \\.\COM5 115200 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
I (202) esp_image: segment 3: paddr=000baf98 vaddr=3fc90bESP-ROM:esp32c3-api1-20210207
Build:Feb  7 2021
rst:0x15 (USB_UART_CHIP_RESET),boot:0xf (SPI_FAST_FLASH_BOOT)
Saved PC:0x403891ce
0x403891ce: vPortSetInterruptMask at C:/Users/Peter/esp/esp-idf/components/freertos/FreeRTOS-Kernel/portable/riscv/port.c:346

SPIWP:0xee
mode:DIO, clock div:1
load:0x3fcd5820,len:0x1738
load:0x403cc710,len:0xae8
load:0x403ce710,len:0x2e38
entry 0x403cc71a
I (15) boot: ESP-IDF HEAD-HASH-NOTFOUND 2nd stage bootloader
I (15) boot: compile time Aug  7 2023 22:41:04
I (15) boot: chip revision: v0.3
I (19) boot.esp32c3: SPI Speed      : 80MHz
I (24) boot.esp32c3: SPI Mode       : DIO
I (29) boot.esp32c3: SPI Flash Size : 2MB
I (33) boot: Enabling RNG early entropy source...
I (39) boot: Partition Table:
I (42) boot: ## Label            Usage          Type ST Offset   Length
I (50) boot:  0 nvs              WiFi data        01 02 00009000 00006000
I (57) boot:  1 phy_init         RF data          01 01 0000f000 00001000
I (65) boot:  2 factory          factory app      00 00 00010000 00100000
I (72) boot: End of partition table
I (76) esp_image: segment 0: paddr=00010020 vaddr=3c090020 size=1fa40h (129600) map
I (105) esp_image: segment 1: paddr=0002fa68 vaddr=3fc90600 size=005b0h (  1456) load
I (106) esp_image: segment 2: paddr=00030020 vaddr=42000020 size=8af70h (569200) map
I (202) esp_image: segment 3: paddr=000baf98 vaddr=3fc90bb0 size=02284h (  8836) load
I (204) esp_image: segment 4: paddr=000bd224 vaddr=40380000 size=10444h ( 66628) load
I (225) boot: Loaded app from partition at offset 0x10000
I (226) boot: Disabling RNG early entropy source...
I (437) cpu_start: Unicore app
I (437) cpu_start: Pro cpu up.
I (446) cpu_start: Pro cpu start user code
I (446) cpu_start: cpu freq: 160000000 Hz
I (446) cpu_start: Application information:
I (449) cpu_start: Project name:     udp_server
I (454) cpu_start: App version:      1
I (458) cpu_start: Compile time:     Aug  7 2023 22:58:34
I (464) cpu_start: ELF file SHA256:  1d9e2e66b...
I (470) cpu_start: ESP-IDF:          HEAD-HASH-NOTFOUND
I (476) cpu_start: Min chip rev:     v0.3
I (480) cpu_start: Max chip rev:     v0.99 
I (485) cpu_start: Chip rev:         v0.3
I (490) heap_init: Initializing. RAM available for dynamic allocation:
I (497) heap_init: At 3FC96ED0 len 00045840 (278 KiB): DRAM
I (503) heap_init: At 3FCDC710 len 00002950 (10 KiB): STACK/DRAM
I (510) heap_init: At 50000010 len 00001FD8 (7 KiB): RTCRAM
I (517) spi_flash: detected chip: generic
I (521) spi_flash: flash io: dio
W (525) spi_flash: Detected size(4096k) larger than the size in the binary image header(2048k). Using the size in the binary image header.
I (538) sleep: Configure to isolate all GPIO pins in sleep state
I (545) sleep: Enable automatic switching of GPIO sleep configuration
I (552) app_start: Starting scheduler on CPU0
I (557) main_task: Started on CPU0
I (557) main_task: Calling app_main()
I (567) pp: pp rom version: 9387209
I (567) net80211: net80211 rom version: 9387209
I (577) wifi:wifi driver task: 3fc9fd20, prio:23, stack:6656, core=0
I (587) wifi:wifi firmware version: 13c0fa6
I (587) wifi:wifi certification version: v7.0
I (587) wifi:config NVS flash: enabled
I (587) wifi:config nano formating: disabled
I (587) wifi:Init data frame dynamic rx buffer num: 32
I (597) wifi:Init management frame dynamic rx buffer num: 32
I (597) wifi:Init management short buffer num: 32
I (607) wifi:Init dynamic tx buffer num: 32
I (607) wifi:Init static tx FG buffer num: 2
I (617) wifi:Init static rx buffer size: 1600
I (617) wifi:Init static rx buffer num: 10
I (617) wifi:Init dynamic rx buffer num: 32
I (627) wifi_init: rx ba win: 6
I (627) wifi_init: tcpip mbox: 32
I (637) wifi_init: udp mbox: 6
I (637) wifi_init: tcp mbox: 6
I (637) wifi_init: tcp tx win: 5744
I (647) wifi_init: tcp rx win: 5744
I (647) wifi_init: tcp mss: 1440
I (657) wifi_init: WiFi IRAM OP enabled
I (657) wifi_init: WiFi RX IRAM OP enabled
I (667) phy_init: phy_version 970,1856f88,May 10 2023,17:44:12
I (707) wifi:mode : softAP (60:55:f9:7b:22:f9)
I (707) wifi:Total power save buffer number: 16
I (707) wifi:Init max length of beacon: 752/752
I (707) wifi:Init max length of beacon: 752/752
I (717) esp_netif_lwip: DHCP server started on interface WIFI_AP_DEF with IP: 192.168.4.1
I (717) example: UDP server listening
I (727) main_task: Returned from app_main()
```

可以看到ESP32C3工作在AP模式，UDP server已经启动，ip是`192.168.4.1`，端口是`12345`。

然后编写一段python代码对其进行测试：

```python
import socket
import timeit

client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

server_ip = '192.168.4.1'
server_port = 12345
server_address = (server_ip, server_port)

message = b"e0a9N3jFpHrX1ohMwR7skB4cbY6DdLAzIxT5WnlG8SOqjEYvQJCZpgi2uUftVKbmT1qArv2NPOxtcgjK19r5WQv0FuoZiBAsfads"

# Number of iterations
num_iterations = 100

# Function to send and receive data
def send_receive_data():
    client_socket.sendto(message, server_address)
    data, _ = client_socket.recvfrom(1024)

# Measure the time taken for data transmission
time_taken = timeit.timeit(send_receive_data, number=num_iterations)

# Calculate communication speed (data size per second)
data_size = len(message) * num_iterations
communication_speed = data_size / time_taken

print("UDP Communication Speed:", communication_speed, "bytes per second")

client_socket.close()
```

这段python代码不严谨地测试了通信速率，发送一段100个字节的数据，esp32c3收到后会立即返回，然后计算时间。

连接到esp32c3，运行一下，server输出：

```powershell
I (14447) wifi:new:<1,1>, old:<1,1>, ap:<1,1>, sta:<255,255>, prof:1
I (14447) wifi:station: 3c:46:d8:dc:0f:4a join, AID=1, bgn, 40U
I (14467) esp_netif_lwip: DHCP server assigned IP to a client, IP is: 192.168.4.2
I (17687) wifi:<ba-add>idx:2 (ifx:1, 3c:46:d8:dc:0f:4a), tid:0, ssn:70, winSize:64
I (26847) example: Received 100 bytes from 192.168.4.2:61159
I (26847) example: e0a9N3jFpHrX1ohMwR7skB4cbY6DdLAzIxT5WnlG8SOqjEYvQJCZpgi2uUftVKbmT1qArv2NPOxtcgjK19r5WQv0FuoZiBAsfads
I (26857) example: Received 100 bytes from 192.168.4.2:61159
I (26867) example: e0a9N3jFpHrX1ohMwR7skB4cbY6DdLAzIxT5WnlG8SOqjEYvQJCZpgi2uUftVKbmT1qArv2NPOxtcgjK19r5WQv0FuoZiBAsfads
I (26887) example: Received 100 bytes from 192.168.4.2:61159
...
```

client输出：

```powershell
UDP Communication Speed: 5490.807948230027 bytes per second
```

运行多次，结果都差不多，在5.5KB/s左右，这个结果应该是远低于phy速率的，跟配置有关，这种测量方法本身也不能反应真实物理速率，但是跟实际使用场景比较接近，可以作为参考。

打印输出有多耗时呢？将以下语句注释掉重新编译：

```c
ESP_LOGI(TAG, "Received %d bytes from %s:%d", rx_len, inet_ntoa(client_addr.sin_addr), ntohs(client_addr.sin_port));
ESP_LOGI(TAG, "%s", rx_buffer);
```

再次运行python代码，最快的一次输出：

```powershell
UDP Communication Speed: 25118.326154934355 bytes per second
```

差不多快了4倍。
