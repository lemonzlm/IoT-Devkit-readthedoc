
# WebSocket over WiFi

## 概述

WebSocket是一种在单个TCP连接上进行全双工通信的协议。WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

## 特征

- 支持基于 TCP 的 WebSocket、带有 mbedtls 的 TLS
- 使用 URI 轻松设置

- 多个实例（一个应用程序中可以有多个客户端）

## 配置

- 支持ws,wss计划

- WebSocket 示例：

  - ws://echo.websocket.org：基于 TCP 的 WebSocket，默认端口 80


  - wss://echo.websocket.org：基于 SSL 的 WebSocket，默认端口 443

## 发送文本数据

WebSocket 客户端支持以文本数据帧的形式发送数据，它通知应用层负载数据是编码为 UTF-8 的文本数据。例：

```c
esp_websocket_client_send_text(client, data, len, portMAX_DELAY);
```

## API 参考

### 功能

```c
esp_websocket_client_handle_tesp_websocket_client_init(constesp_websocket_client_config_t *config)
```

启动 Websocket 会话 该函数必须是第一个调用的函数，它返回一个 esp_websocket_client_handle_t，您必须将其用作接口中其他函数的输入。当操作完成时，此调用必须有对 esp_websocket_client_destroy 的相应调用。

返回

- esp_websocket_client_handle_t
- 如果有任何错误，则为 NULL


参数

- [in] config: 配置

```c
esp_err_tesp_websocket_client_set_uri(esp_websocket_client_handle_tclient, const char *uri)
```

为客户端设置 URL，执行此行为时，URL 中的选项将替换旧选项 如果客户端已连接，则必须在设置 URI 之前停止 WebSocket 客户端。

返回

- esp_err_t

参数

- [in] client： 客户端
- [in] uri: URI

```c
esp_err_t esp_websocket_client_start( esp_websocket_client_handle_tclient)
```

打开 WebSocket 连接。

返回

- esp_err_t

参数

- [in] client： 客户端

```c
esp_err_t esp_websocket_client_stop( esp_websocket_client_handle_tclient)
```

在没有 websocket 关闭握手的情况下停止 WebSocket 连接。

此 API 停止 ws 客户端并直接关闭 TCP 连接，而不发送关闭帧。使用 esp_websocket_client_close() 以干净的方式关闭连接是一个好习惯。

注意：

- 无法从 websocket 事件处理程序调用


返回

- esp_err_t

参数

- [in] client： 客户端

```c
esp_err_t esp_websocket_client_destroy( esp_websocket_client_handle_tclient)
```

销毁 WebSocket 连接并释放所有资源。此函数必须是调用会话的最后一个函数。它与 esp_websocket_client_init 函数相反，必须使用与 esp_websocket_client_init 调用返回的输入相同的句柄来调用。这可能会关闭此句柄已使用的所有连接。

注意：

- 无法从 websocket 事件处理程序调用


返回

- esp_err_t

参数

- [in] client： 客户端

```c
esp_err_tesp_websocket_client_close(esp_websocket_client_handle_tclient, TickType_t timeout)
```

以干净的方式关闭 WebSocket 连接。

客户端发起的干净关闭顺序：

- 客户端发送 CLOSE 帧

- 客户端等待服务器回显 CLOSE 帧

- 客户端等待服务器关闭连接

- 客户端停止的方式与esp_websocket_client_stop()


注意：

- 无法从 websocket 事件处理程序调用


返回

- esp_err_t

参数

- [in] client： 客户端
- [in] timeout: RTOS 计时超时等待

```c
esp_err_tesp_websocket_client_close_with_code(esp_websocket_client_handle_tclient, int code, const char *data, int len, TickType_t timeout)
```

使用自定义代码/数据以干净的方式关闭 WebSocket 连接关闭顺序与 esp_websocket_client_close() 相同

注意：

- 无法从 websocket 事件处理程序调用


返回

- esp_err_t

参数

- [in] client： 客户端
- [in] code: 关闭状态码定义在 RFC6455 section-7.4

- [in] data：关闭消息的附加数据

- [in] len: 附加数据的长度

- [in] timeout: RTOS 计时超时等待

```c
esp_websocket_client_is_connected（esp_websocket_client_handle_tclient）
```

检查 WebSocket 客户端连接状态。

返回

- true

- false

参数

- [in] client: 客户端句柄

❗以上为部分常见常用API，更多请参考乐鑫官方 ESP-IDF编程指南-API参考-协议

## Websocket 示例应用程序

这个示例将展示如何通过 websocket 进行设置和通信。

## 配置项目

命令行界面

打开项目配置菜单 ( idf.py menuconfig)

Clion界面

选择示例的文件夹并用Clion打开

添加各项配置（参考Clion下ESP-IDF的配置与开发）

选择 menuconfig 并运行

- 在“Example Connection Configuration”菜单下配置 Wi-Fi 或以太网。
- 在“Example Connection Configuration”下配置 websocket 端点 URI，如果选择“WEBSOCKET_URI_FROM_STDIN”，则示例应用程序将连接到它从标准输入读取的 URI（用于测试）

## 构建和烧录

构建项目并将其烧写到板上，然后运行监控工具查看串行输出：

命令行界面：

idf.py -p PORT flash monitor
（要退出串行监视器，请键入Ctrl-]。）

Clion界面：

选择 flash 并运行

选择 monitor 并运行

## 示例输出

```bash
I (482) system_api: Base MAC address is not set, read default base MAC address from BLK0 of EFUSE
I (2492) example_connect: Ethernet Link Up
I (4472) tcpip_adapter: eth ip: 192.168.2.137, mask: 255.255.255.0, gw: 192.168.2.2
I (4472) example_connect: Connected to Ethernet
I (4472) example_connect: IPv4 address: 192.168.2.137
I (4472) example_connect: IPv6 address: fe80:0000:0000:0000:bedd:c2ff:fed4:a92b
I (4482) WEBSOCKET: Connecting to ws://echo.websocket.org...
I (5012) WEBSOCKET: WEBSOCKET_EVENT_CONNECTED
I (5492) WEBSOCKET: Sending hello 0000
I (6052) WEBSOCKET: WEBSOCKET_EVENT_DATA
W (6052) WEBSOCKET: Received=hello 0000

I (6492) WEBSOCKET: Sending hello 0001
I (7052) WEBSOCKET: WEBSOCKET_EVENT_DATA
W (7052) WEBSOCKET: Received=hello 0001

I (7492) WEBSOCKET: Sending hello 0002
I (8082) WEBSOCKET: WEBSOCKET_EVENT_DATA
W (8082) WEBSOCKET: Received=hello 0002

I (8492) WEBSOCKET: Sending hello 0003
I (9152) WEBSOCKET: WEBSOCKET_EVENT_DATA
W (9162) WEBSOCKET: Received=hello 0003
```

