---
title: 从BNO055传感器获取IMU数据
date: 2023-06-02 11:17:28
categories:
tags:
    - 嵌入式
    - mcu
    - 硬件
    - 算法
    - diy
    - 自动驾驶
---

最近学习惯导相关知识，BNO055是博世的绝对方向传感器，它结合了传感器数据和微处理器来过滤和组合数据，为用户提供空间中的绝对方向。

## 关于BNO055传感器

BNO055使用三个三轴传感器同时测量切向加速度（通过加速度计），旋转加速度（通过陀螺仪）和局部磁场强度（通过磁力计）。然后，可以将数据发送到外部微处理器，或使用运行专有融合算法的M0+微处理器在传感器内部进行分析。然后，用户可以选择以各种格式从传感器请求数据。

该芯片还具有中断功能，可以在发生某些运动（方向变化、突然加速等）时通知主机微控制器。

![框图](https://imgs.boringhex.top/blog/20230601213704.png)

<!-- more -->

传感器必须在使用前进行校准，读寄存器保存当前的校准状态。校准后，校准偏移可以写入传感器，然后传感器在下次通电时立即可以使用。

观看下面的视频，了解如何校准传感器。

https://weixin.qq.com/sph/AO5mIz

主机微控制器可以在非融合模式下从传感器（加速度计、陀螺仪和/或磁力计）请求任何或所有数据，并且可以在融合模式下请求绝对和相对方向（角度或四元数）。

传感器可以返回以 $m/s^2$ 或 mg ($1mg = 9.81 \times 10^{-3} m/s^2$); 磁场强度（mT）;陀螺仪数据以度或弧度每秒为单位（分别为 DPS 和 RPS），以度或弧度为单位的欧拉角或四元数; 和以°C或°F为单位的温度。所有选项均在unit_selection寄存器中设置（数据表中的表 3-11，PDF 第 30 页）。

## 欧拉角 vs 四元数

如果要为运动范围有限的系统设计传感器解决方案，则可以使用欧拉角。但是，如果您正在设计一种可以在空间中定位任何地方的传感器，则应使用四元数。

### 欧拉角

欧拉角允许围绕垂直轴旋转三次的对象（x-y-x、x-z-x、y-x-y、y-z-y、z-x-z、z-y-z、x-y-z、x-z-y、y-x-z、y-z-x、y-z-x、z-x-y、z-y-x）的物体的简单可视化。

![欧拉角示例](https://imgs.boringhex.top/blog/20230601215335.png)

只要轴至少部分垂直，它们就足够了。但是，当轴旋转时，存在一个角度，其中两个轴可以描述相同的旋转 - 产生称为万向节锁定的条件。当万向节锁定发生时，如果没有外部参考，就不可能重新定向。

![此动画有三个万向节（显示为红色、绿色和蓝色实心圆柱段）以及可用的旋转（显示为红色、绿色和蓝色透明球面月亮）。当内部绿色云台平面与红色云台平面对齐时，红蓝云台旋转轴重叠，云台锁定发生（浅黄色背景表示）。](https://imgs.boringhex.top/blog/GimbalsInSphericalPlot_GimbalLock_ExternalRings.webp)

使用四元数时不存在云台锁定的问题。

### 四元数

四元数是由威廉·汉密尔顿于1843年发明的，作为乘除三个数字的一种方式。几十年来，它们慢慢失宠，并在核时代看到了复兴，并再次通过现代计算机图形编程进行了振兴。四元数由四个数字组成：一个标量和一个三分量向量。

![四元数](https://imgs.boringhex.top/blog/20230601220845.png)

其中 w、x、y 和 z 都是实数，i、j 和 k 是四元数单位。

通常，w、x、y 和 z 保持在 -1 和 1 之间的范围内，并且$\sqrt{w^2+x^2+y^2+z^2}=1$。

这四个数字在一次旋转中简洁地重新定向矢量，无论长度是否发生变化。

![蓝色和红色矢量的单位长度。橙色是将蓝色矢量旋转为红色矢量所需的旋转。](https://imgs.boringhex.top/blog/20230601221119.png)

正态变换矩阵由九个数字组成，涉及三角函数的应用。四元数由四个数字组成，全部小于或等于 1。可以将四元数转换为正交变换矩阵，但由于与万向锁相关的数学性质，从旋转矩阵转换为四元数稍微困难一些。

![将四元数转换为 3x3 正交旋转矩阵的方法。](https://imgs.boringhex.top/blog/20230601221236.png)

下面的代码片段演示了如何创建 3×3 变换矩阵以及四元数的横滚角、俯仰角和偏航角。

```arduino
/* Create Rotation Matrix rm from Quaternion */
double rm[3][3];

rm[1][1] = quat.w()*quat.w() + quat.x()*quat.x() - quat.y()*quat.y() - quat.z()*quat.z();   
rm[1][2] = 2*quat.x()*quat.y() - 2*quat.w()*quat.z();            
rm[1][3] = 2*quat.x()*quat.z() + 2*quat.w()*quat.y();
rm[2][1] = 2*quat.x()*quat.y() + 2*quat.w()*quat.z();       
rm[2][2] = quat.w()*quat.w() - quat.x()*quat.x() + quat.y()*quat.y() - quat.z()*quat.z();          
rm[2][3] = 2*quat.y()*quat.z() - 2*quat.w()*quat.x();     
rm[3][1] = 2*quat.x()*quat.z() - 2*quat.w()*quat.y();       
rm[3][2] = 2*quat.y()*quat.z() + 2*quat.w()*quat.x();            
rm[3][3] = quat.w()*quat.w() - quat.x()*quat.x() - quat.y()*quat.y() + quat.z()*quat.z();

/* Display Rotation Matrix */
Serial.print(rm[1][1],5);Serial.print("  \t");
Serial.print(rm[1][2],5);Serial.print("  \t");
Serial.println(rm[1][3],5);
Serial.print(rm[2][1],5);Serial.print("  \t");
Serial.print(rm[2][2],5);Serial.print("  \t");
Serial.println(rm[2][3],5);
Serial.print(rm[3][1],5);Serial.print("  \t");
Serial.print(rm[3][2],5);Serial.print("  \t");
Serial.println(rm[3][3],5);

/* Create Roll Pitch Yaw Angles from Quaternions */
double yy = quat.y() * quat.y(); // 2 Uses below

double roll = atan2(2 * (quat.w() * quat.x() + quat.y() * quat.z()), 1 - 2*(quat.x() * quat.x() + yy));
double pitch = asin(2 * quat.w() * quat.y() - quat.x() * quat.z());
double yaw = atan2(2 * (quat.w() * quat.z() + quat.x() * quat.y()), 1 - 2*(yy+quat.z() * quat.z()));

/*  Convert Radians to Degrees */
float rollDeg  = 57.2958 * roll;
float pitchDeg = 57.2958 * pitch;
float yawDeg   = 57.2958 * yaw;

/*  Display Roll, Pitch, and Yaw in Radians and Degrees*/
Serial.print("Roll:");  Serial.print(roll,5);  Serial.print(" Radians \t"); Serial.print(rollDeg,2);  Serial.println(" Degrees");
Serial.print("Pitch:"); Serial.print(pitch,5); Serial.print(" Radians \t"); Serial.print(pitchDeg,2); Serial.println(" Degrees");
Serial.print("Yaw:");   Serial.print(yaw,5);   Serial.print(" Radians \t"); Serial.print(yawDeg,2);   Serial.println(" Degrees");
```

今天有些困了，明天继续。。。
