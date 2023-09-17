# UART

## 概述

通用异步接收器/发送器 (UART) 是一种硬件功能，它使用广泛采用的异步串行通信接口（例如 RS232、RS422、RS485）来处理通信（即时序要求和数据帧）。

ESP32 芯片具有三个 UART 控制器（UART0、UART1 和 UART2），每个控制器都具有一组相同的寄存器，以简化编程并提高灵活性。

每个 UART 控制器可独立配置波特率、数据位长度、位顺序、停止位数量、奇偶校验位等参数。所有控制器都兼容不同制造商的 UART 设备，还可以支持红外数据关联协议（红外线）。

## 基本步骤

以下步骤描述了如何使用 UART 驱动程序的功能和数据类型在 ESP32 和其他 UART 设备之间建立通信。

1. 设置通讯参数- 设置波特率、数据位、停止位等。
2. 设置通信引脚- 分配用于连接到设备的引脚。
3. 驱动程序安装- 为 UART 驱动程序分配 ESP32 的资源。
4. 运行 UART 通信- 发送/接收数据
5. 使用中断- 触发特定通信事件的中断
6. 删除驱动程序- 如果不再需要 UART 通信，则释放分配的资源

步骤 1 到 3 包括配置阶段。第 4 步是 UART 开始运行的地方。步骤 5 和 6 是可选的。

### 设置通讯参数

调用函数`uart_param_config()`并将结构体`uart_config_t`传递给它`uart_config_t`。该`uart_config_t`结构应包含所有必需的参数。示例代码如下。

```c
const uart_config_t uart_config = {
    .baud_rate = 115200,
    .data_bits = UART_DATA_8_BITS,
    .parity = UART_PARITY_DISABLE,
    .stop_bits = UART_STOP_BITS_1,
    .flow_ctrl = UART_HW_FLOWCTRL_DISABLE,
    .source_clk = UART_SCLK_APB,
    //从上到下分别为：波特率、数据位、奇偶校验控制、停止位、硬件流控模式、时钟选择
};

uart_param_config(UART_NUM_1, &uart_config);
```

### 设置通信引脚

设置通信参数后，配置 UART 设备将连接到的物理 GPIO 引脚。调用该函数`uart_set_pin()`并指定驱动程序应将 Tx、Rx、(RTS 和 CTS) 信号到的 GPIO 引脚号。

```c
//定义UART引脚
#define TXD_PIN (GPIO_NUM_4)
#define RXD_PIN (GPIO_NUM_5)

uart_set_pin(UART_NUM_1, TXD_PIN, RXD_PIN, UART_PIN_NO_CHANGE, UART_PIN_NO_CHANGE);
```

### 驱动安装

设置好通信引脚后，通过调用安装驱动程序`uart_driver_install()`并指定以下参数：

- Tx 环形缓冲区的大小

- Rx 环形缓冲区的大小

- 事件队列句柄和大小

- 分配中断的标志

该函数将为 UART 驱动程序分配所需的内部资源。

```c
uart_driver_install(UART_NUM_1, RX_BUF_SIZE * 2, 0, 0, NULL, 0);
```

### 传输

准备好要传输的数据后，调用函数`uart_write_bytes()`并将数据缓冲区的地址和数据长度传递给它。该函数会将数据复制到 Tx 环形缓冲区（立即或在有足够空间可用后），然后退出。当 Tx FIFO 缓冲区中有可用空间时，中断服务程序 (ISR) 将数据从 Tx 环形缓冲区移动到后台的 Tx FIFO 缓冲区。下面的代码演示了这个函数的使用。

```c
// 将数据写入 UART。
char* test_str = "This is a test string.\n";
uart_write_bytes(uart_num, (const char*)test_str, strlen(test_str));
```

功能`uart_write_bytes_with_break()`类似于`uart_write_bytes()`但在传输结束时增加了一个串行中断信号。“串行中断信号”意味着将 Tx 线保持在低电平的时间超过一个数据帧。

```c
// 向 UART 写入数据，以中断信号结束。
uart_write_bytes_with_break(uart_num, "test break\n",strlen("test break\n"), 100);
```

将数据写入 Tx FIFO 缓冲区的另一个函数是`uart_tx_chars()`。与 不同`uart_write_bytes()`，此函数在空间可用之前不会阻塞。相反，它将写入所有可以立即放入硬件 Tx FIFO 的数据，然后返回写入的字节数。

有一个“伴侣”功能`uart_wait_tx_done()`可以监控 Tx FIFO 缓冲区的状态，并在它为空时返回。

```c
// 等待数据包发送
const uart_port_t uart_num = UART_NUM_2;
ESP_ERROR_CHECK(uart_wait_tx_done(uart_num, 100)); // wait timeout is 100 RTOS ticks (TickType_t)
```

### 接收

一旦 UART 接收到数据并保存在 Rx FIFO 缓冲区中，就需要使用 函数检索数据`uart_read_bytes()`。在读取数据之前，您可以通过调用来检查 Rx FIFO 缓冲区中可用的字节数`uart_get_buffeed_data_len()`。下面给出了使用这些函数的示例。

```c
// 从 UART 读取数据。
const uart_port_t uart_num = UART_NUM_2;
uint8_t data[128];
int length = 0;
ESP_ERROR_CHECK(uart_get_buffered_data_len(uart_num, (size_t*)&length));
length = uart_read_bytes(uart_num, data, length, 100);
```

如果不再需要 Rx FIFO 缓冲区中的数据，您可以通过调用清除缓冲区 uart_flush()。

### 软件流控制

如果硬件流控关闭，可以分别使用 和 函数手动设置 RTS 和 DTR 信号电`uart_set_rts()`平`uart_set_dtr()`。

### 通讯方式选择

UART 控制器支持多种通信模式。可以使用该功能选择模式`uart_set_mode()`。选择特定模式后，UART 驱动程序将相应地处理连接的 UART 设备的行为。例如，它可以使用 RTS 线控制 RS485 驱动芯片，实现半双工 RS485 通信。

```c
// 将 UART 设置为 rs485 半双工模式
ESP_ERROR_CHECK(uart_set_mode(uart_num, UART_MODE_RS485_HALF_DUPLEX));
```

### 使用中断

在特定的 UART 状态或检测到的错误之后，可以生成许多中断。`ESP32 技术参考手册> UART 控制器 (UART) > UART 中断和UHCI 中断[ PDF ]`中提供了可用中断的完整列表。`uart_enable_intr_mask()`您可以通过调用或`uart_disable_intr_mask()`分别启用或禁用特定的中断。所有中断的掩码都可以作为`UART_INTR_MASK`.

默认情况下，该`uart_driver_install()`函数安装驱动程序的内部中断处理程序来管理 Tx 和 Rx 环形缓冲区，并提供高级 API 函数，如事件（见下文）。也可以使用 注册一个较低级别的中断处理程序`uart_isr_register()`，并使用 再次释放它`uart_isr_free()`。在这种情况下，一些使用 Tx 和 Rx 环形缓冲区、事件等的 UART 驱动程序函数将不会自动工作 - 必须直接在 ISR 中处理中断。在自定义处理程序实现中，使用清除中断状态位`uart_clear_intr_status()`。

API 提供了一种方便的方法来处理本文档中讨论的特定中断，方法是将它们包装到专用函数中：

- 事件检测：其中定义了几个事件`uart_event_type_t`，可以使用 FreeRTOS 队列功能向用户应用程序报告。您可以在驱动程序安装`uart_driver_install()`中描述的调用时启用此功能。在 peripherals/uart/uart_events 中可以找到使用事件检测的示例。

- FIFO 空间阈值或传输超时：当 Tx 和 Rx FIFO 缓冲区填充特定数量的字符或发送或接收数据超时时，它们会触发中断。要使用这些中断，请执行以下操作：

  - `uart_intr_config_t`通过在结构中输入并调用来配置缓冲区长度和超时的各个阈值`uart_intr_config()`

  - 使用函数启用中断`uart_enable_tx_intr()`和`uart_enable_rx_intr()`

  - 使用相应的函数禁用这些中断`uart_disable_tx_intr()`或`uart_disable_rx_intr()`

- 模式检测：在检测到相同字符的“模式”被重复接收/发送多次时触发的中断。此功能在示例`peripherals/uart/uart_events`中演示。例如，它可用于检测命令字符串后跟在命令字符串末尾添加的特定数量的相同字符（“模式”）。可以使用以下功能：

  - 使用配置和启用此中断`uart_enable_pattern_det_intr()`

  - 使用禁用中断`uart_disable_pattern_det_intr()`

### 宏

API 还定义了几个宏。例如，`UART_FIFO_LEN`定义硬件 FIFO 缓冲区的长度；`UART_BITRATE_MAX`给出 UART 控制器等支持的最大波特率。

### 删除驱动程序

`uart_driver_install()`如果不再需要与之建立的通信，可以通过调用来删除驱动程序以释放分配的资源`uart_driver_delete()`。

## 实验内容

示例演示两个异步任务如何使用相同的 UART 接口进行通信。可以使用此示例为串行通信开发更复杂的应用程序。

该示例启动了两个 FreeRTOS 任务：

1. 第一个任务`Hello world`通过 UART 定期发送。

2. 第二个任务任务监听、接收和打印来自 UART 的数据。

### 所需硬件

ESP32 开发板、microUSB 线、电脑，一根母对母头杜邦线。

### 硬件接线

代码中可配置的`RXD_PIN`和`TXD_PIN`（默认情况下是 GPIO4 和 GPIO5）需要使用杜邦线进行**短接**。

### 源代码参考

参见[参考资料与源代码](../../reference.rst)

### 代码调试与示例输出

使用 idf.py 以及 clion 管理项目，编码，build, flash, 以及 monitor。

运行 monitor 后，你将从监控控制台收到以下串口打印输出：

```bash
...
I (3261) TX_TASK: Wrote 11 bytes
I (4261) RX_TASK: Read 11 bytes: 'Hello world'
I (4261) RX_TASK: 0x3ffb821c   48 65 6c 6c 6f 20 77 6f  72 6c 64                 |Hello world|
...
```

## API 参考

[UART API](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/peripherals/uart.html)
