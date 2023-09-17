
# BT HCI主机控制接口

BlueTooth 的HCI(主机控制接口)包括三种类型控制接口：

- 通过UART使用HCI
- 通过virtual HCI 层使用HCI
- 混合方式

**本实验使用最基础的UART方式**

## UART HCI 控制器

这使用 UART 作为 HCI 控制器，这要求 UART 设备强制支持 RTS/CTS。

可以通过 `idf.py menuconfig` 来配置串口号和串口波特率。

### 源代码参考

参见[参考资料与源代码](../../reference.rst)

### UART分配

`UART0` 用作正常输出或由 IDF 监视器使用

`UART1` 或 `UART2` 用于传送 HCI 消息

需要 `UART1` 或 `UART2` 的 `RTS` 和 `CTS` 线。引脚 5、18、23、19 分别用作 `UART1` 或 `UART2` 的 `TxD`、`RxD`、`CTS`、`RTS` 引脚。

## 实验步骤

### 配置项目

```bash
idf.py menuconfig
```

`UART`编号配置：`HCI UART(H4) Options > UART Number for HCI`

`UART`波特率配置：`HCI UART(H4) Options > UART Baudrate for HCI`

### 构建和烧录

构建项目并将其烧写到板上，然后运行监控工具查看串行输出：

#### 命令行界面：

```shell
idf.py -p PORT flash monitor 
```

（要退出串行监视器，请键入Ctrl-]。）

#### Clion界面：

选择 flash 并运行

选择 monitor 并运行

### 示例输出

该示例重置 HCI UART 传输并启用蓝牙控制器。

```bash
I (442) CONTROLLER_UART_HCI: HCI UART1 Pin select: TX 5, RX 18, CTS 23, RTS 19
I (442) BTDM_INIT: BT controller compile version [078d492]
I (452) system_api: Base MAC address is not set
I (452) system_api: read default base MAC address from EFUSE
I (462) phy_init: phy_version 4670,719f9f6,Feb 18 2021,17:07:07
```

在这些输出发生后，HCI 消息可以通过 `UART1` 或 `UART2` 进行通信。（本例中为 `UART1`）
