---
title: 从BNO055传感器获取IMU数据-2
date: 2023-06-09 11:58:57
categories:
tags:
    - 嵌入式
    - mcu
    - 硬件
    - 算法
    - diy
    - 自动驾驶
---

# 从BNO055传感器获取IMU数据-2

在前面的文章 [从BNO055传感器获取IMU数据-1](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651481499&idx=1&sn=e46768e63e2d88640e836ec15187c8db&chksm=84ad7358b3dafa4edd8a858f5d9cd8b60c5e4b192d331bdc61cee6bd07c5e24261bec7053769#rd) 中介绍了BNO055传感器，今天继续讲解应用示例。

## 传感器与Arduino接口

我从某宝购买了固定在带有支持组件的开发板上的 BNO055 传感器。从 Digi-Key 或贸泽购买 BNO055 并将其焊接到 7.5×4.4mm 28 引脚 LGA 至 DIP 转换器上，以便在无焊试验板上进行原型设计，可以节省一点钱。但是对于运输后的边际成本节省，我不推荐它。

要开始使用 BNO055 与 Arduino 接口，请按照以下步骤操作：

1. 连接电源、接地、SDA 和 SCL
2. 打开Arduino IDE，然后单击Sketch→Include Library→Manage Libraries
3. 搜索并安装“Adafruit BNO055”和“Adafruit Sensor”
4. 打开并编辑文件→示例→Adafruit BNO055→Raw Data以注释掉欧拉角部分并取消注释四元数部分，或复制并粘贴下面的代码片段。

<!-- more -->

```arduino
#include 
#include 
#include 
#include 

/*
This program is an abridged version of Adafruit BNO055 rawdata.ino available after installing the Adafruit BNO055 library
File→Examples→Adafruit BNO055→Raw Data
  Connections on Arduino Uno
  =========================================================================
  SCL to analog 5 | SDA to analog 4 | VDD to 3.3V DC | GND to common ground
*/

#define BNO055_SAMPLERATE_DELAY_MS (100)          // Delay between data requests

Adafruit_BNO055 bno = Adafruit_BNO055();          // Create sensor object bno based on Adafruit_BNO055 library

void setup(void)
{
  Serial.begin(115200);                           // Begin serial port communication
  if(!bno.begin())                                // Initialize sensor communication
  {  
    Serial.print("Ooops, no BNO055 detected ... Check your wiring or I2C ADDR!");  
  }
  delay(1000);
  bno.setExtCrystalUse(true);                     // Use the crystal on the development board
}

void loop(void)
{
  imu::Quaternion quat = bno.getQuat();           // Request quaternion data from BNO055

  Serial.print(quat.w(), 4);  Serial.print("\t"); // Print quaternion w
  Serial.print(quat.x(), 4);  Serial.print("\t"); // Print quaternion x
  Serial.print(quat.y(), 4);  Serial.print("\t"); // Print quaternion y
  Serial.print(quat.z(), 4);  Serial.println();   // Print quaternion z

  delay(BNO055_SAMPLERATE_DELAY_MS);              // Pause before capturing new data
}
```

该程序只需通过I2C与BNO055通信，并通过UART端口将数据流回计算机。四元数数据作为制表符分隔的数据发送回，每个四元数后带有换行符。

请记住，在校准传感器之前，数据无效。

## 四元数数据样本

对于以下示例，我从 BNO055 请求四元数数据，因为它以随机方向放置在办公桌附近。你的以通过在单词`quaternion`后面的括号中输入逗号分隔值来手动解释 [WolframAlpha.com](http://www.wolframalpha.com/input/?i=quaternion(0.403,0.413,0.085,0.812)) 数据(例如，"[`quaternion`(0.403， 0.414， 0.085， 0.812)](http://www.wolframalpha.com/input/?i=quaternion(0.403,0.413,0.085,0.812))")

| W          | X          | Y          | Z          |
|------------|------------|------------|------------|
| 0.40344238 | 0.41363525 | 0.08508301 | 0.81176757 |

上面的数字是一个四元数，描述了传感器相对于参考方向的当前方向。

![[来自 WolframAlpha.com](http://www.wolframalpha.com/input/?i=quaternion(0.403,0.413,0.085,0.812)) 的图像显示对象的默认和实际方向](https://imgs.boringhex.top/blog/20230603110301.png)

![[来自 WolframAlpha.com](http://www.wolframalpha.com/input/?i=quaternion(0.403,0.413,0.085,0.812)) 的图像显示了旋转轴和从默认位置到达当前位置所需的旋转量](https://imgs.boringhex.top/blog/20230603110420.png)

当然没有必要每次在互联网上查看你的四元数数据，但可以选择性仔细检查数据是很好的实践，尤其是在入门学习阶段。

## 使用 Mathematica 捕获数据

[Mathematica](https://www.wolfram.com/mathematica/index.php.zh)是一个多功能的计算机程序，几乎可以处理你能想象到的任何数据。对于那些感兴趣的人，我整理了几行代码，演示如何从设备接收数据以及如何使用Mathematica通过一些基于四元数的函数来评估数据。下面的代码是为Windows编写的，因此Linux和Mac用户可能必须更改输入设备行（以bConnect开头的行）。

![Mathematica 笔记本的屏幕截图，源码可通过下面的下载链接获得](https://imgs.boringhex.top/blog/20230603111050.png)

链接: https://pan.baidu.com/s/1AI1Npdj_jgKbg5o0esHtMQ?pwd=mp63 提取码: mp63 复制这段内容后打开百度网盘手机App，操作更方便哦

Mathematica允许实时和事后收集和处理数据。对于此演示，我选择了一个程序，该程序从串行缓冲区收集数据，将其转换为旋转矩阵，并使用旋转矩阵重新定向参考球体中的箭头。

首先单击“连接Arduino”，然后单击“来自缓冲区的数据”。我没有合并任何数据验证或错误检查，因此如果数据区域为空白或格式错误，唯一的选择是重新收集数据。

Mathematica能够在数据通过串行端口到达时读取和处理数据，但对于此演示，存储在缓冲区中的30个左右的测量值应该足以看到程序是如何工作的。

![从BNO055发送的数据在Mathematica中进行分析，并用于重新定向参考球中的箭头](https://imgs.boringhex.top/blog/Hughes_Quaternion_RandomMovement.gif)

## 控制两轴云台

BNO055非常适合机器人使用。作为示例应用，我将使用 BNO055 控制安装在基于伺服的两轴云台上的激光器。该代码应该允许无缝引入第三个旋转轴，供任何有幸拥有旋转轴的读者使用。

像以前一样连接BNO055并添加伺服器，连接到数字引脚9-11。如果您使用云台来固定相机，请考虑升级到 Alorium XLR8 板。伺服依赖于精确的计时，如果Arduino正在处理竞争任务，则可能导致抖动。XLR8是由FPGA制成的Arduino的直接替代品。它有一个库，可以从单独的“XLR8tor”（加速器）块控制伺服系统，以实现稳定和流畅的伺服运动。

![电路的布线概述](https://imgs.boringhex.top/blog/20230603114605.png)

![Arduino Uno R3 替代品：XLR8 vs Sparkfun “Redboard” Arduino Uno R3](https://imgs.boringhex.top/blog/20230603114636.png)

```arduino
//---- Included Libraries ----//
#include                            // I²C library
#include                            // trig functions
#include                 // Base library for sensors
#include                 // BNO055 specific library
#include                // Vector, Matrix, and IMUMath library
//#include                         // Standard Servo library
#include                       // XLR8 servo library
#include                       // XLR8 accelerated floating point math

#define BNO055_SAMPLERATE_DELAY_MS (50)     // Set pause between samples

//---- Variable Declaration ----//

boolean debug = true;                       // true/false = extra/no information over serial

int rollPin  = 9;                           // Digital pin for roll
int yawPin   = 10;                          // Digital pin for yaw
int pitchPin = 11;                          // Digital pin for pitch

float roll, pitch, yaw;                     // Variable to hold roll, pitch, yaw information

Adafruit_BNO055 bno = Adafruit_BNO055();    // Use object bno to hold information

Servo rollServo;                            // Create servo rollServo
Servo pitchServo;                           // Create servo pitchServo
Servo yawServo;                             // Create servo yawServo

void setup(void) {

  rollServo.attach(rollPin);                // The rollServo is connected at rollPin
  pitchServo.attach(pitchPin);              // The pitchServo is connected at pitchPin
  yawServo.attach(yawPin);                  // The yawServo is connected at yawPin

  Serial.begin(115200);                     // Create serial connection at 115,000 Baud

  if (!bno.begin())                         // Attempt communication with sensor
  {
    Serial.print("Ooops, no BNO055 detected ... Check your wiring or I2C ADDR!");
  }

  delay(100);                               // Wait 0.1 seconds to allow it to initialize
  bno.setExtCrystalUse(true);               // Tell sensor to use external crystal
}

//---- Main Program Loop ----//
void loop() {

  //---- Request Euler Angles from Sensor ----//
  imu::Vector<3> euler = bno.getVector(Adafruit_BNO055::VECTOR_EULER);

  if (debug) {                             // If debug is true, send information over serial
    Serial.print("Measured Euler Roll-Pitch-Yaw");
    Serial.print("\t   yaw: "); Serial.print(euler.x()); Serial.print("\t");
    Serial.print("\t pitch: "); Serial.print(euler.z()); Serial.print("\t");
    Serial.print("\t  roll: "); Serial.print(euler.y()); Serial.println();
  }
  /* Remap information from the sensor over the 0° - 180° range of the servo
     The Yaw values are between 0° to +360°
     The Roll values are between -90° and +90°
     The Pitch values are between -180° and +180°
  */
  int servoYaw   = map(euler.x(),   0, 360, 0, 180);
  int servoRoll =  map(euler.y(),  -90, 90, 0, 180);
  int servoPitch = map(euler.z(), -180, 180, 0, 180);

  if (debug) {                             // If debug is true, send information over serial
    Serial.print("Measured Euler Roll-Pitch-Yaw");
    Serial.print("\t   Yaw Servo: "); Serial.print(servoYaw);   Serial.print("\t");
    Serial.print("\t Pitch Servo: "); Serial.print(servoPitch); Serial.print("\t");
    Serial.print("\t  Roll Servo: "); Serial.print(servoRoll);  Serial.println();
  }
  // If debug is true, send information over serial
  if (debug) {
    Serial.println("Calculated Servo Roll-Pitch-Yaw");
    Serial.print("\t roll:");  Serial.print(servoRoll, DEC);  Serial.print("\t");
    Serial.print("\t pitch:"); Serial.print(servoPitch, DEC); Serial.print("\t");
    Serial.print("\t yaw:");   Serial.print(servoYaw, DEC);   Serial.println();
  }

  rollServo.write(servoRoll);              // Send mapped value to rollServo
  pitchServo.write(servoPitch);            // Send mapped value to rollServo
  yawServo.write(servoYaw);                // Send mapped value to rollServo

  delay(BNO055_SAMPLERATE_DELAY_MS);       // Wait before rerunning loop
}
```

源码下载👇

链接: https://pan.baidu.com/s/1NmL1emNqXAdRGV0XEq8u5Q?pwd=wdu1 提取码: wdu1 复制这段内容后打开百度网盘手机App，操作更方便哦 

视频演示：

<iframe src="//player.bilibili.com/player.html?bvid=BV1hh4y1d7HT&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

## 总结

BNO055是一款易于使用的惯性测量单元，可集成到各种应用中，从机器人稳定（四轴飞行器，倒立摆等）到相机稳定和导航（包括航位推算）。

与其他输出原始测量数据的 9 自由度系统不同，BNO055 为主机微控制器过滤和合成数据，从而释放处理器带宽并消除编程中的猜测。
