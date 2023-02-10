---
title: ChatGPT编写MCU代码
date: 2023-02-10 10:23:48
categories:
tags:
  - chatgpt
  - ai
  - mcu
---

最近都在玩儿chatGPT，我今天也凑了个热闹：

<iframe src="//player.bilibili.com/player.html?bvid=BV1ms4y1Y77i&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

<!-- more -->

## 编写一个arduino控制步进电机的代码

> write a program for arduino: 1. controll a stepper 2. rotate 20 degree/s

```c
#include <Stepper.h>

const int stepsPerRevolution = 200; // number of steps per revolution of the stepper motor
Stepper myStepper(stepsPerRevolution, 8, 9, 10, 11); // create a Stepper object using the Stepper library and the given pin numbers

void setup() {
  myStepper.setSpeed(20); // set the speed of the stepper motor to 20 degrees per second
}

void loop() {
  myStepper.step(stepsPerRevolution / 18); // rotate the stepper motor by 20 degrees
  delay(50); // wait for 50 milliseconds before the next step
}

```

然后还非常人性的给解释下这段代码具体干了啥，有哪些地方需要根据实际情况再修改。

> Note: This code assumes that the stepper motor is connected to the specified pins and that the steps per revolution of the motor is 200. If your stepper motor has a different number of steps per revolution, you will need to adjust the stepsPerRevolution constant accordingly.

## STM32L476使用FreeRTOS创建闪烁LED任务

> for stm32l476, using freertos create a task, that blink a led every second, and print message via uart1

```c
#include "stm32l4xx_hal.h"
#include "FreeRTOS.h"
#include "task.h"

#define LED_PORT GPIOC
#define LED_PIN GPIO_PIN_13

UART_HandleTypeDef huart1;

void vTaskBlinkLED(void *pvParameters)
{
    while (1)
    {
        HAL_GPIO_TogglePin(LED_PORT, LED_PIN);
        vTaskDelay(pdMS_TO_TICKS(1000));
        HAL_UART_Transmit(&huart1, (uint8_t *)"LED Blink\r\n", 11, 100);
    }
}

int main(void)
{
    /* Initialize the UART1 */
    huart1.Instance = USART1;
    huart1.Init.BaudRate = 115200;
    huart1.Init.WordLength = UART_WORDLENGTH_8B;
    huart1.Init.StopBits = UART_STOPBITS_1;
    huart1.Init.Parity = UART_PARITY_NONE;
    huart1.Init.Mode = UART_MODE_TX_RX;
    huart1.Init.HwFlowCtl = UART_HWCONTROL_NONE;
    huart1.Init.OverSampling = UART_OVERSAMPLING_16;
    HAL_UART_Init(&huart1);

    /* Initialize the LED pin */
    __HAL_RCC_GPIOC_CLK_ENABLE();
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    GPIO_InitStruct.Pin = LED_PIN;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
    HAL_GPIO_Init(LED_PORT, &GPIO_InitStruct);

    /* Create the task */
    xTaskCreate(vTaskBlinkLED, "TaskBlinkLED", configMINIMAL_STACK_SIZE, NULL, 1, NULL);

    /* Start the FreeRTOS scheduler */
    vTaskStartScheduler();

    /* Should never reach this point */
    while (1) {}
    return 0;
}
```

> Note: This code assumes that the STM32L476 is set up and configured to use FreeRTOS, and the necessary drivers for the LED and UART1 peripherals are properly initialized. The code also assumes that the LED is connected to GPIOC, Pin 13.

## raspberry pi pico检测光强

> for raspberry pi pico, write a program to detect the light intensity

```c
from machine import ADC
import time

adc = ADC(0) # create an ADC object using ADC channel 0

while True:
    light_level = adc.read() # read the ADC value
    print("Light Intensity:", light_level)
    time.sleep(1) # wait for 1 second before the next reading

```

> Note: This code assumes that the light sensor is connected to ADC channel 0 of the Raspberry Pi Pico. If the light sensor is connected to a different channel, you will need to adjust the ADC channel number accordingly.

目前是可以跟ai结对编程，估计再进化进化，就能坐在边上看ai编程了😀
