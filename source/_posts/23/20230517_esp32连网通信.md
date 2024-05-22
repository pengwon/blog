---
title: esp32连网通信
date: 2023-05-17 12:06:57
categories:
tags:
    - esp32
    - micropython
    - python
---

使用esp32开发程序，非常重要的功能就是使用wifi，下面我们就讲一下esp32连接wifi的方法。

在我们能接触的WiFi网络中，`Wi-Fi`设备通常处于以下两种角色中的一种：

* 提供无线接入的设备，称之为Access Point(`AP`) 无线接入点，家里常见的路由器就是结合WiFi和网络路由功能的AP，AP和AP可以相互连接。

- 连接到AP的设备，称之为Station(`STA`) 无线终端，像手机、电脑等需要联网的设备都处于STA模式，这个模式不允许其他设备接入。

> 说明：
>
> 一个设备可能既支持AP又支持STA，但是一般设备同一时间，WiF模块只能启用其中的一种模式，像手机既能连接外面的WiFi，又能自己开热点让别的设备连接，但是每当开启热点，WiFi就会自动关闭，这是因为手机在同一时间只能支持AP和STA一种。

<!-- more -->

## 1. 官方文档

官方文档对esp32实现[wifi功能的文档链接](https://docs.micropython.org/en/latest/esp32/quickref.html#networking)，如下：

![](https://imgs.boringhex.top/blog/202305082006717.png)

实现WiFi连接功能，需要使用到`network`模块：

不管是STA还是AP都是基于network的WLAN，针对LAN我们以后再讲。

**STA的实现方法**：

```python
import network

wlan = network.WLAN(network.STA_IF) # create station interface
wlan.active(True)       # activate the interface
wlan.scan()             # scan for access points
wlan.isconnected()      # check if the station is connected to an AP
wlan.connect('ssid', 'key') # connect to an AP
wlan.config('mac')      # get the interface's MAC address
wlan.ifconfig()         # get the interface's IP/netmask/gw/DNS addresses
```

ssid：就是需要链接的WiFi的名称。

key：就是需要链接的WiFi的密码。

> 说明：
>
> 现在的路由器都有2.4g和5G两个频段的信号，但是esp32只支持2.4G的信号。

官网提供了一个创建STA的demo：

```python
def do_connect():
    import network
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    if not wlan.isconnected():
        print('connecting to network...')
        wlan.connect('ssid', 'key')
        while not wlan.isconnected():
            pass
    print('network config:', wlan.ifconfig())
```

**AP的实现方法**：

```python
import network

ap = network.WLAN(network.AP_IF) # create access-point interface
ap.config(ssid='ESP-AP') # set the SSID of the access point
ap.config(max_clients=10) # set how many clients can connect to the network
ap.active(True)         # activate the interface
```

AP功能用于让其他设备连接esp32，这个功能不常用，我们以后会在补充里面介绍。

## 2. 创建一个STA的基本流程

esp32不管是UDP还是TCP通信，都需要两个模块的支持。

network：用于连接WiFi，获取wifi分配的ip等。

socket：用于设备之间的通信，当连接网络后，下一步就是建立和其他设备的通信。

下面我们创建一个WiFi类，实现STA连接：

```python
import network
import time

class Wifi():
    """
    只实现了STA模式的WiFi链接封装
    """


    def __init__(self):
        self.wlan = network.WLAN(network.STA_IF)

 

    """
    复位WiFi模块
    """
    def Init_wifi(self):
        if  self.wlan.isconnected():
            self.wlan.active(False) 
            print("wifi disconnect,please Reset Configuration ")

    """
    获取WiFi状态
    """    
    def status(self):
        return self.wlan.status()
    

    """
    获取WiFi配置
    """
    def get_wifi_config(self):
        if self.wlan.isconnected():
            return self.wlan.ifconfig()


    """
    扫描WiFi列表
    """
    def wifi_scan(self):
        if wifi.status() != network.STAT_CONNECTING:
            self.wlan.active(True) # 开启WiFi 
        return self.wlan.scan()

    
    """
    连接WiFi
    """       
    def connet_wifi(self, ssid, password):
        count = 10 # 10秒内未连接到网络，则连接超时
        self.wlan.active(True) # 开启WiFi
        if not self.wlan.isconnected():
            self.wlan.connect(ssid,password) 
            while not self.wlan.isconnected():
                print('connecting to network...')
                time.sleep(1)
                count -= 1 
                if count == 0: # 连接超时
                    self.wlan.active(False) # 关闭WiFi
                    print("Connecting  Timeout,Please check the account password!")
                    break 
        if self.wlan.isconnected():
            # 连接成功，则打印被分配的ip等信息
            print("ip:" ,self.wlan.ifconfig()[0])
            print("subnet:" ,self.wlan.ifconfig()[1])
            print("gateway:" ,self.wlan.ifconfig()[2])
            print("dns:" ,self.wlan.ifconfig()[3])
            return True
        else:
            # 未成功连接，则返回False
            return False


if __name__ == "__main__":
    wifi = Wifi() # 创建WiFi对象
    wifi_list = wifi.wifi_scan() # 扫描WiFi列表
    print(wifi_list)
    
    link_wifi = wifi.connet_wifi('K3', 'zhao0312') # 连接WiFi
    if(link_wifi == True):
        print("connet wifi is ok")
```
在调用wlan.connect()之后，设备将在默认情况下永远重试连接，即使身份验证失败或范围内没有AP，因此我们不需要重复connect，只需要执行一次，自己设置一个等待超时就可以了。当然若不想这样也可以通过调wlan.config(reconnects=n)来更改，其中n是所需的重新连接尝试次数(0表示不会重试，-1将恢复尝试永远重新连接的默认行为)。

wlan.isconnected()可以判断当前是否连接成功，当然也可以使用wlan.status()将返回网络状态，调用wlan.connect()之后，网络将处于network.STAT_CONNECTING表示正在连接状态，直到连接成功或接口被禁用状态才会改变。

一旦网络建立，我们可以通过socket模块像往常写python程序一样创建和使用TCP/UDP套接字，使用urequests模块可以方便的实现HTTP请求。

## 3. 测试udp通信

> 提示：
>
> 使用micropython开发esp32，要求开发者要有python基础。

首先将电脑安装一个网络调试助手软件，这里推荐使用NetAssist.exe。下载地址：[下载链接](http://www.cmsoft.cn/resource/102.html)；

然后找一个能保证PC和ESP32都能正常连接的路由器，把电脑连接上路由器，然后在电脑的cmd控制台输入ipconfig，看一下路由器分配的ip地址，如下：

![](https://imgs.boringhex.top/blog/202305082007239.png)

这个ip地址将会在后面使用到。打开电脑上下载一个网络助手等待备用。

将上面连接wifi的程序封装成wifi.py，然后创建一个test_socket.py文件，这时esp内目录如下：

![](https://imgs.boringhex.top/blog/202305082007037.png)

然后将下面内容添加到test_socket.py文件中，内容如下：

```python
from wifi import Wifi

from socket import *
import time

# 我电脑的ip
server_addr = "192.168.2.103"
# 随便选一个端口，建议端口范围2000~65535
server_port = 5000

def creation_udp_client():
    # 1. 创建udp套接字
    udp_socket = socket(AF_INET, SOCK_DGRAM)
    # 2. 准备服务器收方的地址
    dest_addr = (server_addr, server_port)
    
    # 3. 从键盘获取数据
    send_data = "hello world"
    # 4. 发送数据到指定的电脑上
    udp_socket.sendto(send_data.encode('utf-8'), dest_addr)
    # 5. 关闭套接字
    udp_socket.close()

if __name__ == "__main__":
    wifi = Wifi() # 创建WiFi对象
    link_wifi = wifi.connet_wifi('K3', 'zhao0312') # 连接WiFi
    if(link_wifi == True):
        print("connet wifi is ok")
        while True:        
            creation_udp_client()
            time.sleep(5)

```

点击ide上面的运行按钮，如下：

![](https://imgs.boringhex.top/blog/202305082007875.png)

然后打开网络助手：

选择ip和端口号与test_socket.py文件设置的一致，就会收到由esp32发过来的消息，如下：

![](https://imgs.boringhex.top/blog/202305082008411.png)

到此实验完成，若需要让程序开机运行，则只需要将test_socket.py内容copy到boot.py文件中即可。
