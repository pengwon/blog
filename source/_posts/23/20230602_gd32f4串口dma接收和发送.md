---
title: gd32f4串口dma接收和发送
date: 2023-06-02 10:49:09
categories:
tags:
    - c
    - stm32
    - mcu
    - 嵌入式
---

## 1、通常的实现方式介绍

1. 环形缓冲区+定时器超时中断的方式
   - 优点
     - 环形缓冲区可以接收多帧数据
     - 数据帧超时间隔可以设置
   - 缺点
     - 设备任务比较繁重时，使用中断接收可能会丢失数据。尤其是在长时间关闭中断或者串口中断优先级不高时
     - 频繁进出中断。在使用RTOS的系统中，每收到一个数据就会进行一次任务到中断的切换和中断到任务的切换
2. 使用串口接收空闲中断+DMA的方式
   - 优点
     - 不会频繁在任务和中断之间切换，效率会更高
     - 一般不会丢失数据
   - 缺点
     - 空闲中断的时间对于同一个波特率来说是固定的，但某些时候1个字节的接收时间太短，不能作为数据帧接收完成的标志

<!-- more -->

## 2、接收超时中断的相关内容

GD32F4系列的单片机串口除了空闲中断外，还有可配置时间的接收超时中断（STM32F4系列没有此中断、STM32L4系列有），使能配置在**USART_CTL3**寄存器的**RTIE**，如下图

![USART_CTL3寄存器](https://imgs.boringhex.top/blog/202305291649389.png)

接收超时标志在**USART_STAT1**寄存器的**RTF**，如下图

![USART_STAT1寄存器](https://imgs.boringhex.top/blog/202305291652213.png)

超时时间在**USART_RT**寄存器中**RT[23:0]**配置，如下图

![USART_RT寄存器](https://imgs.boringhex.top/blog/202305291653279.png)

其中**RT**为**24**位，单位是波特率的位时间，即bps。举个例子，如果串口的参数配置位8-N-1（一个开始位、8个数据位、没有奇偶校验位、一个停止位），即一个字节的传输需要10个波特率的比特位，RT设置为100，则表示10（100/10）个字节的传输超时时间。

## 3、接收超时中断+DMA实现

​	示例中用到了**串口2**、**DMA0**的**通道1**（串口2的DMA接收）和**通道3**（串口2的DMA发送），串口2的**Tx**为**PB10**、**Rx**为**PB11**。

- 串口接收数据缓冲区

  ```c
  #define BLE_UART USART2						///< 串口2
  #define RX_SERIAL_BUF_SIZE 256				///< 串口2的接收缓冲区大小
  static char recv_buf[RX_SERIAL_BUF_SIZE];	///< receive buffer
  static uint8_t uart2_rx_state = 0;			///< 串口接收完成标志。1表示接收完成
  static uint8_t uart2_tx_state = 0;			///< 串口DMA发送完成标志。1表示发送完成
  static uint16_t uart2_rx_len = 0;			///< 串口实际接收的数据长度
  ```

- 串口中断处理函数

  ```c
  /**
    * @brief uart2的中断处理函数
    *		只关心接收超时中断
    *
    * @retval void
    * 
    * @note 
    */
  void USART2_IRQHandler(void)
  {
  	/* UART接收超时中断 */
  	if ((usart_interrupt_flag_get(BLE_UART, USART_INT_FLAG_RT) != RESET) &&
  	        (usart_flag_get(BLE_UART, USART_FLAG_RT) != RESET))
  	{
  		/* disable DMA and reconfigure */
  		dma_channel_disable(DMA0, DMA_CH1);	//关闭DMA,在没有读取该接收帧数据之前禁止DMA再接收数据
  		dma_flag_clear(DMA0, DMA_CH1, DMA_FLAG_FTF);  // 清除DMA传输完成标志位  
  
  		/* Clear receiver timeout flag */
  //		usart_flag_clear(BLE_UART, USART_FLAG_RT);
  		usart_interrupt_flag_clear(BLE_UART,USART_INT_FLAG_RT);
  		usart_data_receive(BLE_UART); /* 清除接收完成标志位 */
  
  		// 设置接收的数据长度
  		uart2_rx_len = get_uart2_dma_recv_data_size();
  
  		/* 接收超时后，说明一帧数据接收完毕，置接收完成标志 */
  		uart2_rx_state = 1;
  	}
  }
  ```

- DMA0_Channel1传输完成中断（用于串口的接收完成），正常情况下，此中断不会发生。

  ```c
  /**
    * @brief DMA0_Channel1传输完成中断
    *		用于串口DMA接收
    * 	
    * @retval void
    * 
    * @note 因用到了串口的接收超时中断方式，正常情况下，串口的DMA接收完成不会发生
    */
  void DMA0_Channel1_IRQHandler(void)  
  {
  	if(dma_interrupt_flag_get(DMA0, DMA_CH1, DMA_INT_FLAG_FTF))
  	{
  	    dma_interrupt_flag_clear(DMA0, DMA_CH1, DMA_INT_FLAG_FTF);
  	//	uart2_rx_state = 1;
  		dma_channel_disable(DMA0, DMA_CH1);  // 关闭DMA接收传输
  	}
  }
  ```

- DMA0_Channel3传输完成中断（用于串口的发送完成）

  ```c
  /**
    * @brief DMA0_Channel3传输完成中断
    *		用于BLE模块的串口DMA发送
    *
    * @retval void
    * 
    * @note 
    */
  void DMA0_Channel3_IRQHandler(void)  
  {	
  	if(dma_interrupt_flag_get(DMA0, DMA_CH3, DMA_INT_FLAG_FTF))
  	{
  	    dma_interrupt_flag_clear(DMA0, DMA_CH3, DMA_INT_FLAG_FTF);
  		uart2_tx_state = 1;
  		dma_channel_disable(DMA0, DMA_CH3);  // 关闭DMA发送传输
      }
  }
  ```

- 获取uart2串口DMA接收的数据长度

  ```c
  /**
    * @brief 获取uart2串口DMA接收的数据长度.
    * 	
    * @retval void
    * 
    * @note 
    */
  static unsigned int get_uart2_dma_recv_data_size(void)
  {
      /*
      dma_transfer_number_get(DMA_CH2);是获取当前指针计数值，
      用内存缓冲区大小 - 此计数值 = 接收到的数据长度（这里单位为字节）。
      需要说明下在读取数据长度的时候需要先把接收DMA关闭，读取完了或者是数据处理完了在打开接收DMA，防止在处理的过程中有数据到来而出错。
      */
      return (RT_SERIAL_RB_BUFSZ - (dma_transfer_number_get(DMA0, DMA_CH1)));
  }
  ```

- uart2串口初始化

  ```c
  /**
    * @brief uart2串口初始化.
    *			串口接收通过DMA+接收超时中断实现，设置的超时时间为100个bps
    * 
    * @param baudrate 串口波特率
    *
    * @retval void
    * 
    * @note 
    */
  static void uart2_init(uint32_t baudrate)
  {
  	/*uart dma rx and tx set*/
  	dma_single_data_parameter_struct dma_init_uart;
  
  	/*****************************	配置uart2的gpio	*****************************/
  	/* enable GPIO clock */
  	rcu_periph_clock_enable(RCU_GPIOB);
  	/* connect port to USARTx_Tx */
  	gpio_af_set(GPIOB, GPIO_AF_7, GPIO_PIN_10);	
  	/* connect port to USARTx_Rx */
  	gpio_af_set(GPIOB, GPIO_AF_7, GPIO_PIN_11);
  	/* configure USART Tx as alternate function push-pull */
  	gpio_mode_set(GPIOB, GPIO_MODE_AF, GPIO_PUPD_PULLUP, GPIO_PIN_10);
  	gpio_output_options_set(GPIOB, GPIO_OTYPE_PP, GPIO_OSPEED_50MHZ, GPIO_PIN_10);
  	/* configure USART Rx as alternate function push-pull */
  	gpio_mode_set(GPIOB, GPIO_MODE_AF, GPIO_PUPD_PULLUP, GPIO_PIN_11);
  	gpio_output_options_set(GPIOB, GPIO_OTYPE_PP, GPIO_OSPEED_50MHZ, GPIO_PIN_11);
  
  	/*****************************	配置uart2的参数	*****************************/
  	/* enable USART clock */  
  	rcu_periph_clock_enable(RCU_USART2);
  	/* USART configure */
  	usart_deinit(BLE_UART);
  	usart_oversample_config(BLE_UART, USART_OVSMOD_8);
  	usart_baudrate_set(BLE_UART, baudrate); // 波特率
  	usart_parity_config(BLE_UART, USART_PM_NONE); // 校验位:NONE
  	usart_word_length_set(BLE_UART, USART_WL_8BIT); // 数据位:8
  	usart_stop_bit_set(BLE_UART, USART_STB_1BIT); // 停止位:1
  	usart_receive_config(BLE_UART, USART_RECEIVE_ENABLE); // 打开串口接收功能
  	usart_transmit_config(BLE_UART, USART_TRANSMIT_ENABLE); // 打开串口发送功能
  	// 接收超时设置，100个波特率的比特位
  	usart_receiver_timeout_threshold_config(BLE_UART, 100);
  	usart_interrupt_enable(BLE_UART, USART_INT_RT);
  	usart_receiver_timeout_enable(BLE_UART);
  	/* USART interrupt configuration */
  	nvic_irq_enable(USART2_IRQn, 0, 1);
  	usart_enable(BLE_UART);
  	usart_dma_receive_config(BLE_UART, USART_DENR_ENABLE); // 使能DMA接收功能
  	usart_dma_transmit_config(BLE_UART, USART_DENT_ENABLE); // 使能DMA发送功能
  
  	/*****************************	配置uart2的DMA接收	****************************/
  	/* enable DMA0 */
  	rcu_periph_clock_enable(RCU_DMA0);
  	/* deinitialize DMA channel */
  	dma_deinit(DMA0, DMA_CH1);
  	dma_init_uart.direction = DMA_PERIPH_TO_MEMORY;
  	dma_init_uart.memory0_addr = (uint32_t)(recv_buf); // 存储器地址
  	dma_init_uart.memory_inc = DMA_MEMORY_INCREASE_ENABLE;
  	dma_init_uart.periph_memory_width = DMA_PERIPH_WIDTH_8BIT;
  	dma_init_uart.number = sizeof(recv_buf);
  	dma_init_uart.periph_addr = (uint32_t)&USART_DATA(BLE_UART);
  	dma_init_uart.periph_inc = DMA_PERIPH_INCREASE_DISABLE;
  	dma_init_uart.priority = DMA_PRIORITY_ULTRA_HIGH;
  	dma_init_uart.circular_mode = DMA_CIRCULAR_MODE_DISABLE;
  	dma_single_data_mode_init(DMA0, DMA_CH1, &dma_init_uart);
  	dma_channel_subperipheral_select(DMA0, DMA_CH1, DMA_SUBPERI4);
      
      uart2_rx_state = 0;
      uart2_rx_len = 0;
      
  	//使能通道
  	dma_channel_enable(DMA0, DMA_CH1);
  
  	/*****************************	配置uart2的DMA发送	***************************/
  	/* deinitialize DMA channel */
  	dma_deinit(DMA0, DMA_CH3);
  	dma_init_uart.direction = DMA_MEMORY_TO_PERIPH;
  	dma_init_uart.memory0_addr = RT_NULL;  // 内存基地址
  	dma_init_uart.number = 0;  // len个数据
  	dma_single_data_mode_init(DMA0, DMA_CH3, &dma_init_uart);
  	dma_channel_subperipheral_select(DMA0, DMA_CH3, DMA_SUBPERI4);
  
  //	nvic_irq_enable(DMA0_Channel3_IRQn, 0, 2);
  
  	uart2_tx_state = 0;
  
  	return;
  }
  ```

- 重新配置uart2串口的DMA接收通道

  ```c
  /**
    * @brief 重新配置uart2串口的DMA接收通道
    * 	
    * @retval void
    * 
    * @note 
    */
  static void uart2_dma_rx_refcg(void)
  {
  	/* disable DMA and reconfigure */
  	dma_channel_disable(DMA0, DMA_CH1); //关闭DMA,在没有读取该接收帧数据之前禁止DMA再接收数据
  //	DMA_INTC0(DMA0) |= DMA_FLAG_ADD(DMA_CHINTF_RESET_VALUE, DMA_CH1);
  
  	dma_memory_address_config(DMA0, DMA_CH1, DMA_MEMORY_0, (uint32_t)(recv_buf)); // 存储器地址
  	dma_transfer_number_config(DMA0, DMA_CH1, sizeof(recv_buf););
  	
      uart2_rx_state = 0;
  	uart2_rx_len = 0;
      
  	// 使能通道
  	dma_channel_enable(DMA0, DMA_CH1);
  }
  ```

- ​	DMA串口发送

  串口发送使用DMA方式时，直接调用uart2_sendData_DMA函数即可。等待发送完成时，可以通过等待(uart2_tx_state == 1)实现，或者等待DMA0通道3的DMA_FLAG_FTF置位实现，或者使用RTOS的信号量实现

  ```c
  /**
    * @brief BLE模块的DMA串口发送.
    *		
    * 
    * @param data 发送数据缓冲区地址
    * @param len 发送数据长度
    *
    * @retval void
    * 
    * @note 
    */
  void uart2_sendData_DMA(uint8_t *data, uint32_t len)  
  {
  	/* disable DMA and reconfigure */
  	dma_channel_disable(DMA0, DMA_CH3);
  	dma_flag_clear(DMA0, DMA_CH3, DMA_FLAG_FTF);  // 清除DMA传输完成标志位
  
  	dma_memory_address_config(DMA0, DMA_CH3, DMA_MEMORY_0, (uint32_t)(data)); // 存储器地址
  	dma_transfer_number_config(DMA0, DMA_CH3, len);
  
  //	/* enable DMA0 channel3 transfer complete interrupt */
  //	dma_interrupt_enable(DMA0, DMA_CH3, DMA_CHXCTL_FTFIE);
  //	uart2_tx_state = 0;
  	dma_channel_enable(DMA0, DMA_CH3);  // 使能DMA传输
  
  	// 等待传输完成
  	while(dma_flag_get(DMA0, DMA_CH3, DMA_FLAG_FTF) == RESET)
  	{}
  
  //	// 等待传输完成
  //	while(uart2_tx_state == 0);
  	
  //	printf("uart2_sendData_DMA complete");
  }
  ```

- 接收处理任务

  ```c
  /**
    * @brief  main函数
    * @param  argc
    * @param  argv
    * @note   等待接收数据完成，然后做相应的处理
    * @retval None
    */
  int main(char argc, char *argv[])
  {
  	uart2_init(115200);
      
      while (1)
      {
          // 接收完成一帧数据
          if(uart2_rx_state == 1)
          {
              // 数据处理
              ... ...
              
              // 处理完成后，重新启动串口的DMA接收
              uart2_dma_rx_refcg();
          }
  		
          // 其他处理
          ... ...
      }
  }
  ```
