
# 基于STM32的LoRaWan节点通信

在LoRa AT指令操作章节中，我们使用LoRa模块上的usb接口与直接与电脑通信，但在实际应用中，通常是用另一个带串口的微控制器发送AT指令来配置RAK3172模块，两者配合工作。或者直接使用LoRa模组工作。本章节我们将学习如何使用实验箱上的STM32开发板发送AT指令配置RAK3172模块，从而完成LoRaWan节点通信实验。

为了方便理解和演示实验效果，本章节通过stm32的串口1与电脑的串口助手通信，通过串口1控制stm32的串口2与LoRa模块通信，进行AT指令配置，让LoRa模块连接上chirpstack。

通信流程如图：

![image-20230224104004544](lorawan-stm32.assets/image-20230224104004544.png)

## 实验目的

- 学习AT指令的使用
- 了解实验箱上的RAK3172模块
- 实现LoRa连接上chirpstack并通信

## 准备工作

### 硬件

- 物联网实验箱、LoRaWan网关

### 软件

- Serial Port Terminal

## 实验步骤

### STM32CubeMX创建工程

- 配置usart1和usart2，打开中断（usart2进行同样的配置）

![image-20230224103031152](lorawan-stm32.assets/image-20230224103031152.png)

### 代码编写及运行结果

- 包含头文件，定义全局变量等

```c
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "stdio.h"
#include "string.h"
/* USER CODE END Includes */
//......................................

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */
#define RXBUFFERSIZE  256     //最大接收字节数
/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */
char Uart1_RxBuffer[RXBUFFERSIZE];   //接收数据
uint8_t Uart1_aRxBuffer;			//接收中断缓冲
uint8_t Uart1_Rx_Cnt = 0;		//接收缓冲计数

char Uart2_RxBuffer[RXBUFFERSIZE];   //接收数据
uint8_t Uart2_aRxBuffer;			//接收中断缓冲
uint8_t Uart2_Rx_Cnt = 0;		//接收缓冲计数
/* USER CODE END PM */
//......................................
```

- 先调用一次中断接受函数

```c
  /* USER CODE BEGIN 2 */
  HAL_UART_Receive_IT(&huart1, (uint8_t *)&Uart1_aRxBuffer, 1);
  HAL_UART_Receive_IT(&huart2, (uint8_t *)&Uart2_aRxBuffer, 1);
  /* USER CODE END 2 */
```

- 重定向串口中断回调函数

```c
/* USER CODE BEGIN 4 */
//可接收不定长度的数据，以\r\n为结束位，收到的数据存在Uart1_RxBuffer中
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
    /* Prevent unused argument(s) compilation warning */
    UNUSED(huart);
    /* NOTE: This function Should not be modified, when the callback is needed,
             the HAL_UART_TxCpltCallback could be implemented in the user file
     */
    // uart1
    if(huart->Instance == USART1){
        if(Uart1_Rx_Cnt >= 255)  //溢出判断
        {
            Uart1_Rx_Cnt = 0;
            memset(Uart1_RxBuffer, 0x00, sizeof(Uart1_RxBuffer));
            HAL_UART_Transmit(&huart1, (uint8_t *)"数据溢出\r\n", 10,0xFFFF);
        }
        else
        {
            Uart1_RxBuffer[Uart1_Rx_Cnt++] = Uart1_aRxBuffer;   //接收数据转存
            //0x0A 是 \r 的 ASCII码，0x0D 是 \n 的 ASCII码
            if((Uart1_RxBuffer[Uart1_Rx_Cnt - 1] == 0x0A) && (Uart1_RxBuffer[Uart1_Rx_Cnt - 2] == 0x0D)) //判断结束位，以\r\n结尾为一组数据
            {
                /*
                HAL_UART_Transmit(&huart1, (uint8_t *)&Uart1_RxBuffer, Uart1_Rx_Cnt, 0xFFFF); //将收到的信息发送出去
                while(HAL_UART_GetState(&huart1) == HAL_UART_STATE_BUSY_TX);//检测UART发送结束
                */

                // 将uart1接收到的内容发送给uart2
                HAL_UART_Transmit(&huart2, (uint8_t *)&Uart1_RxBuffer, Uart1_Rx_Cnt, 0xFFFF); //将收到的信息发送出去
                while(HAL_UART_GetState(&huart2) == HAL_UART_STATE_BUSY_TX);//检测UART发送结束

                Uart1_Rx_Cnt = 0;
                memset(Uart1_RxBuffer, 0x00, sizeof(Uart1_RxBuffer)); //清空数组
            }
        }
    }

    // uart2
    if(huart->Instance == USART2){
        if(Uart2_Rx_Cnt >= 255)  //溢出判断
        {
            Uart2_Rx_Cnt = 0;
            memset(Uart2_RxBuffer, 0x00, sizeof(Uart2_RxBuffer));
            HAL_UART_Transmit(&huart1, (uint8_t *)"数据溢出\r\n", 10,0xFFFF);
        }
        else
        {
            Uart2_RxBuffer[Uart2_Rx_Cnt++] = Uart2_aRxBuffer;   //接收数据转存
            //0x0A 是 \r 的 ASCII码，0x0D 是 \n 的 ASCII码
            if((Uart2_RxBuffer[Uart2_Rx_Cnt - 1] == 0x0A) && (Uart2_RxBuffer[Uart2_Rx_Cnt - 2] == 0x0D)) //判断结束位，以\r\n结尾为一组数据
            {
                /*
                HAL_UART_Transmit(&huart2, (uint8_t *)&Uart2_RxBuffer, Uart2_Rx_Cnt, 0xFFFF); //将收到的信息发送出去
                while(HAL_UART_GetState(&huart2) == HAL_UART_STATE_BUSY_TX);//检测UART发送结束
                */

                // 将uart1接收到的内容发送给uart2
                HAL_UART_Transmit(&huart1, (uint8_t *)&Uart2_RxBuffer, Uart2_Rx_Cnt, 0xFFFF); //将收到的信息发送出去
                while(HAL_UART_GetState(&huart1) == HAL_UART_STATE_BUSY_TX);//检测UART发送结束

                Uart2_Rx_Cnt = 0;
                memset(Uart2_RxBuffer, 0x00, sizeof(Uart2_RxBuffer)); //清空数组
            }
        }
    }

    HAL_UART_Receive_IT(&huart1, (uint8_t *)&Uart1_aRxBuffer, 1);   //再次调用中断接收函数
    HAL_UART_Receive_IT(&huart2, (uint8_t *)&Uart2_aRxBuffer, 1);   //再次调用中断接收函数
}
/* USER CODE END 4 */
```

- 实验步骤

打开串口调试助手，发送想要发送的AT指令。以下是让Lora连接上chirpstack的AT指令，具体功能和参数请到官网查询，最需要注意的是DEVEUI和APPKEY，APPEUI没用到的话，跟DEVEUI一致即可。

> 查询固件版本：AT+VER=?\r\n
>
> 配置网络加入模式，0为ABP模式，1为OTAA模式：AT+NJM=1
>
> 配置区域频段，1为CN470，中国所用的频段：AT+BAND=1
>
> 设置信道掩码：AT+MASK=0400
>
> 设置节点工作模式为A：AT+CLASS=A
>
> 配置设备EUI：AT+DEVEUI=2022060615490001
>
> 配置应用程序标识符：AT+APPEUI=2022060615490001
>
> 配置应用程序密钥：AT+APPKEY=20220606154911112222333344445555
>
> 配置确认模式：AT+CFM=1
>
> 让Lora发出加入网络请求：AT+JOIN=1:0:10:8
>
> 让Lora发送数据：AT+SEND=22:12345678

每发送一条AT指令，Lora模块的返回数据都会在串口助手上显示，即可判断Lora模块是否成功接收到AT指令，或者根据返回的信息判断是否成功加入网络，发送数据等。此外，若是使能了Lora模块的命令回显功能，发送的AT指令会一起返回。

- 本次实验到此结束，然而，在实际使用的时候，不可能这样通过人为的形式来判断AT指令是否成功发送和根据Lora的返回判断Lora模块是否成功接收AT指令，而是需要通过编写程序来实现AT指令解析的。大概的功能就是stm32发送AT指令，然后根据Lora模块的返回值来判断Lora模块是否正确地接受到AT指令，在传输数据量比较大的时候，更是需要合理编写串口收发程序，避免溢出和错误，这方面的内容将在以后的章节讲解。
