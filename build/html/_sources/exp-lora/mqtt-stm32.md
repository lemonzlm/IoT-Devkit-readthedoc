# 基于STM32的LoRaWan节点MQTT应用

 在一些物联网场景中，需要在LoRaWAN组网网络中，在自己的服务器上，通过MQTT订阅来获取到节点上报的数据。

## 相关知识

1. LoRaWAN典型网络结构

为了更清楚地MQTT的具体位置，我们需要先对LoRaWAN组网的典型网络结构进行介绍，如图：

![image-20230304192539953](mqtt-stm32.assets/image-20230304192539953.png)

2. MQTT工作原理，如图：

![image-20230304192635350](mqtt-stm32.assets/image-20230304192635350.png)

3. 在LoRaWAN网络中，如何通过MQTT订阅来获取到LoRa节点的数据？如图：

![image-20230304192737996](mqtt-stm32.assets/image-20230304192737996.png)

有些LoRaWAN网关仅仅是网关，但有些LoRaWAN网关内置服务器（NS）。比如，我们所用的RAK网关既是LoRaWAN网关的角色，又是NS的角色。对于这种类型的LoRaWAN网关而言就可以按照以上图的方式来进行组网，构成LoRaWAN+MQTT的网络组合。

在LoRaWAN+MQTT的网络组合中，NS是做为一个发布者，将接收到的LoRa节点数据发布到MQTT Broker上，用户自己的应用程序只要做为订阅者，就可以非常便捷的获取到LoRa节点的数据信息了。

那有同学就会有疑问了，在LoRaWAN网络中，RAK的网关既是LoRaWAN网关的角色，又是NS的角色。那么在MQTT的网络中，RAK的网关就只能是发布者的角色吗？它能不能扮演MQTT Broker的角色呢？

RAK的网关内部集成一个MQTT Broker,也就是说，也能兼职MQTT Broker的角色。使用内部集成有MQTT Broker的LoRaWAN网关，用户无需其他部署，就能在用户自己的应用服务器上通过MQTT订阅到LoRa节点上报的数据信息了，非常方便。

## 实验目的

- 


## 准备工作

- 

### 硬件

- 

### 软件

- 

## 相关电路原理

- 

## 引脚定义与相关API

- 

## 实验步骤

### STM32CubeMX创建工程

### 代码编写及运行结果

