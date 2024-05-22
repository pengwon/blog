---
title: 开源调测利器：I2C电流计 功率计
date: 2023-09-25 10:59:14
categories:
tags:
    - diy
    - 硬件
    - 工具
---

小飞机的板子画成这样：

![esp-plane](https://imgs.boringhex.top/blog/20230917192746.png)

然后就开始溜号了。。。真心希望有小伙伴能来一起搞一下啊~

溜号是因为这次想在马达控制中加入电流闭环，所以就想测一下720空心杯带桨和不带桨状态下用锂电池供电的电流，正当要测时发现手上的万用表没电了，很是恼火。并且我的万用表也无法连续记录数据，这样就无法得到锂电池的放电曲线，所以干脆自己做一个吧！

<!-- more -->

## 方案一

MCU + ADC + OP AMP

有不少专为电流测量的放大器，比如TI的INA181、INA199，如果对分辨率要求不高，现在很多MCU内置12位ADC已经足够用了。这种方案架构比较灵活，可选范围很大。但是需要开发MCU程序，并且电路也不简洁。

## 方案二

数字接口电流计/功率计芯片

这种方案集成度高，电路简洁，但是可选范围变窄了，测量参数指标完全由芯片决定。

因为之前已经做了 [CH347转SPI I2C接口模块](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481723&idx=1&sn=7b6a3534aae340e11f1985ccc646b400&chksm=84ad70b8b3daf9aed715122abb24033a534d7de40e1dac77ef0ab303dfdaaf485c03204480aa#rd)，啥也别说了，方案二走起！

## 原理图

选用了TI的INA226芯片，原理图如下：

![原理图](https://imgs.boringhex.top/blog/20230917194725.png)

### 特性

- 感测总线电压范围：0V - 36V
- 高侧/低侧电流感测
- 电压、电流、功率测量
- 0.1% 增益误差
- 10uV 偏移
- 可配置测量平均值
- 16个可配置I2C地址
- 2.7V - 5.5V 电源供电
- 2路电源轨，可选为负载电源，作为 [CH347高速USB桥接模块](https://github.com/pengwon/ch347-hs-usb-bridge) 扩展时，为 3.3V 和 5V
- Alert指示灯
- 非共地连接时，可选远端电压感测
- 引脚兼容CH347模块，即插即用

## PCB

![3d PCB](https://imgs.boringhex.top/blog/20230917195325.png)

打样归来：

![TOP](https://imgs.boringhex.top/blog/top.jpg)

![BOTTOM](https://imgs.boringhex.top/blog/bottom.jpg)

![CH347扩展](https://imgs.boringhex.top/blog/CH347扩展.jpg)

项目已经上传到GitHub：

https://github.com/pengwon/ina226

## 测试

python API已经编写差不多，连接esp32-c3模块测试下：

![连接ESP32模块测试](https://imgs.boringhex.top/blog/f3a85b531e62af7028a30f330165978.jpg)

测试代码：

```python
sensor = INA226()
print(sensor.get_config())
sensor.set_alert_limit(0x1000)
print(sensor.get_calibration())
print(sensor.get_shunt_voltage(), 'uV')
print(sensor.get_bus_voltage(), 'mV')
print(sensor.get_current(), 'uA')
print(sensor.get_power(), 'mW')
sensor.set_mask_enable('SOL')
print(sensor.get_mask_enable())
sensor.close()
```

输出：

```powershell
{'reset': False, 'avg': 0, 'vbus_ct': 4, 'vsh_ct': 4, 'mode': 7}
2048
595.0 uV
5035.0 mV
29750.0 uA
150.0 mW
{'SOL': True, 'SUL': False, 'BOL': False, 'BUL': False, 'POL': False, 'CNVR': False, 'AFF': False, 'CVRF': True, 'OVF': False, 'APOL': False, 'LEN': False}
```

这个esp32模块中有程序在运行，功耗应该是动态变化的，画个动态图看看：

![10Hz采样](https://imgs.boringhex.top/blog/python_qKqctGsWyX.gif)

后面有时间可以学着搞个gui，毕竟颜值即正义嘛。
