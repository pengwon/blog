---
title: 如何获取MAC地址
date: 2024-10-15 11:24:53
categories:
tags:
    - c
    - 算法
---


在计算机网络中，MAC 地址（媒体访问控制地址）是一个至关重要的概念。它是网络设备的唯一标识符，负责在局域网内实现设备间的通信。本文将深入探讨 MAC 地址的定义、格式、功能、如何获取以及其在网络中的作用。

## 一、什么是 MAC 地址？

MAC 地址是分配给网络接口卡（NIC）的硬件地址，用于在网络中唯一标识设备。与 IP 地址不同，MAC 地址是固定的，不会随网络的变化而改变。每个网络接口都有一个唯一的 MAC 地址，使其能够在局域网内被识别。

### 1. MAC 地址的组成

MAC 地址通常由 48 位二进制数表示，通常以 12 个十六进制数字的形式表示，例如 `00:1A:2B:3C:4D:5E`。其中，前 3 个字节（24 位）由 IEEE 分配，称为组织唯一标识符（OUI），用于标识制造商；后 3 个字节由制造商自行分配，以确保每个设备的 MAC 地址唯一。

### 2. MAC 地址的格式

MAC 地址有两种常见的表示格式：

- 十六进制格式：如 `00:1A:2B:3C:4D:5E` 或 `00-1A-2B-3C-4D-5E`。
- 无分隔符格式：如 `001A2B3C4D5E`。

<!-- more -->

## 二、MAC 地址的功能

MAC 地址在网络通信中起着重要的作用，主要体现在以下几个方面：

### 1. 局域网内唯一标识

每个网络设备（如计算机、打印机、路由器等）都有一个唯一的 MAC 地址，这使得设备能够在局域网中被准确地识别和定位。网络交换机根据 MAC 地址将数据包正确地转发到目标设备。

### 2. 数据包转发

在以太网等局域网协议中，数据包通过 MAC 地址进行转发。当一个设备发送数据时，它会将目标设备的 MAC 地址包含在数据包中，网络交换机根据这些地址将数据包发送到正确的端口。

### 3. 安全性和访问控制

一些网络设备支持基于 MAC 地址的访问控制列表（ACL）。通过设置白名单或黑名单，网络管理员可以限制哪些设备可以访问网络资源，提高网络的安全性。

## 三、如何获取 MAC 地址

获取设备的 MAC 地址的方法因操作系统而异。以下是常见操作系统获取 MAC 地址的步骤：

### 1. Windows 系统

在 Windows 中，可以通过命令提示符获取 MAC 地址：

1. 按 `Win + R`，输入 `cmd` 打开命令提示符。
2. 输入命令 `ipconfig /all`，然后按 Enter。
3. 在结果中查找 "物理地址"（Physical Address），即为 MAC 地址。

一般来说，系统中会有多个网卡，需要自己找到对应的网卡的 MAC 地址。

### 2. macOS 系统

在 macOS 中，可以通过终端获取 MAC 地址：

1. 打开 "终端" 应用（Terminal）。
2. 输入命令 `ifconfig`，然后按 Enter。
3. 查找 `en0` 或 `en1` 接口下的 `ether` 行，后面的值即为 MAC 地址。

### 3. Linux 系统

在 Linux 中，可以使用以下命令获取 MAC 地址：

1. 打开终端。
2. 输入命令 `ip addr show` 或 `ifconfig`，然后按 Enter。
3. 查找对应网络接口的 `link/ether` 行，后面的值即为 MAC 地址。

## 四、MAC 地址的局限性

虽然 MAC 地址在局域网中非常重要，但它也有一些局限性：

### 1. 不可路由性

MAC 地址只能用于局域网内的通信，无法在互联网上路由。为了实现跨网络通信，数据包需要使用 IP 地址。

### 2. 可伪造性

由于 MAC 地址是硬件地址，可以通过软件手段进行伪造，这可能导致安全隐患。在某些情况下，攻击者可能会伪造自己的 MAC 地址，以便绕过网络安全措施。

### 3. 隐私问题

设备的 MAC 地址在网络中是公开的，可能会被用来跟踪用户的活动。因此，一些操作系统提供了 MAC 地址随机化功能，以增强用户的隐私保护。

## 五、总结

MAC 地址是网络通信中不可或缺的重要组成部分，它为每个设备提供了唯一的标识，使得局域网内的设备能够正确地相互通信。尽管 MAC 地址在网络管理和安全性方面发挥了重要作用，但也存在一些局限性和隐私问题。因此，网络管理员在设计和管理网络时，应充分考虑 MAC 地址的特性，以确保网络的高效性和安全性。

上面讲了如何获取本机的 MAC 地址，如果想获取局域网内其他设备的 MAC 地址，可以使用网络扫描工具，如 `nmap`、`Angry IP Scanner` 等，来扫描局域网内的设备并获取其 MAC 地址。最常见的，我们可能需要获取WiFi AP的MAC地址，如果有路由器的管理权限，可以在路由器的管理界面中查看，如果设备容易查看，一般路由器的背面会有标签，上面会有WiFi的SSID和密码，也会有MAC地址。如果条件受限，可以用以下命令获取：

### Windows 系统

1. 打开命令提示符（Command Prompt）。
2. 输入以下命令，然后按 Enter：
   ```sh
   netsh wlan show interfaces
   ```
3. 查找 `BSSID` 行，后面的值即为当前连接的WiFi的MAC地址。

### macOS 系统

1. 打开 "终端" 应用（Terminal）。
2. 输入以下命令，然后按 Enter：
   ```sh
   /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -I
   ```
3. 查找 `BSSID` 行，后面的值即为当前连接的WiFi的MAC地址。

### Linux 系统

1. 打开终端。
2. 输入以下命令，然后按 Enter：
   ```sh
   iwconfig
   ```
3. 查找当前无线网络接口（通常是 `wlan0` 或 `wlp2s0`）下的 `Access Point` 行，后面的值即为当前连接的WiFi的MAC地址。

### 示例

#### Windows 示例

```sh
C:\> netsh wlan show interfaces

    Name                   : Wi-Fi
    Description            : Intel(R) Wireless-AC 9560 160MHz
    GUID                   : 12345678-1234-1234-1234-123456789012
    Physical address       : 00:1A:2B:3C:4D:5E
    State                  : connected
    SSID                   : MyWiFiNetwork
    BSSID                  : 01:23:45:67:89:AB
    Network type           : Infrastructure
    Radio type             : 802.11ac
    Authentication         : WPA2-Personal
    Cipher                 : CCMP
    Connection mode        : Auto Connect
    Channel                : 6
    Receive rate (Mbps)    : 300
    Transmit rate (Mbps)   : 300
    Signal                 : 100%
    Profile                : MyWiFiNetwork
```

#### macOS 示例

```sh
$ /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -I

     agrCtlRSSI: -40
     agrExtRSSI: 0
    agrCtlNoise: -90
    agrExtNoise: 0
          state: running
        op mode: station
     lastTxRate: 866
        maxRate: 866
lastAssocStatus: 0
    802.11 auth: open
      link auth: wpa2-psk
          BSSID: 01:23:45:67:89:AB
           SSID: MyWiFiNetwork
            MCS: 9
        channel: 36,80
```

#### Linux 示例

```sh
$ iwconfig

wlp2s0

   

 IEEE 802.11  ESSID:"MyWiFiNetwork"  
          Mode:Managed  Frequency:2.437 GHz  Access Point: 01:23:45:67:89:AB   
          Bit Rate=72.2 Mb/s   Tx-Power=22 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:on
          Link Quality=70/70  Signal level=-39 dBm  
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```

通过以上步骤，你可以在不同操作系统上查看当前连接的WiFi的MAC地址。

> 注意：本文与 [昨天的文章](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651482778&idx=1&sn=7d31ad0eeb90f830f3e36b3720f72fc9&chksm=84ad7c59b3daf54fb4b27752d1987009d86410eba2ed8bcdf76fa7b53505e8769b70d5213df1#rd) 没有任何关联，纯技术探讨。
