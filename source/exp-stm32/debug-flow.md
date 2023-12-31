# 基本调试流程

有时候我们辛辛苦苦写好的程序可能会运行崩溃，或不符合预期💢💢或者单纯地想要去查看一些变量，而 CLion 里面是支持全功能的单步断点调试的，也能在代码里直接观察变量的值，非常方便~


## 实验目的

掌握 CLion IDE 的基本调试流程

## 准备工作

- 完成 IDE 环境搭建


- Ubuntu 中安装 openocd


### 硬件

- 物联网实验箱

- 能运行 Ubuntu20.04 的PC

### 软件

- Ubuntu20.04 镜像文件
- STM32CubeMX
- VMware
- CLion

## 相关电路原理

LED2 的一端接 GND，另一端接 STM32 的 GPIO，当 GPIO 设置为高电平时，灯亮，反之，灯灭。

![image-20220728154606991](debug-flow.assets/image-20220728154606991.png)

![image-20220728154710007](debug-flow.assets/image-20220728154710007.png)

## 引脚定义与相关API

- 引脚定义：LED2 与 STM32 的 PB14 相连
- 相关 API：与 IDE环境搭建 章节相同

## 实验步骤

在 IDE 环境搭建章节中，我们新建了一个工程，这一章节就利用这个工程来完成基本调试流程

### 断点调试

- 打开工程，用 USB 线连接 STM32 与 PC，程序与上一章节保持一致

- 在此处打上断点后，点击右上角开始调试

![image-20220729092711991](debug-flow.assets/image-20220729092711991.png)

![image-20220729093427714](debug-flow.assets/image-20220729093427714.png)

> :exclamation: 注意
>
> 在 keil 中，是先开始调试，再打断点，而在 CLion 中，需要先打断点，再开始调试，这一点需要注意，顺序不小心错了的同学会出现如图情况，解决方法是退出调试（点击右上角的红色正方形按钮），打上断点，再开始调试。
>
> ![image-20220729093743221](debug-flow.assets/image-20220729093743221.png)

### 变量观察

- 在此处打上断点后开始调试，程序就会运行到此处

![image-20220729094623896](debug-flow.assets/image-20220729094623896.png)

- 点击步入后，会跳转到该函数内部，可观察变量的值

![image-20220729094820051](debug-flow.assets/image-20220729094820051.png)

至此，基本调试流程已介绍完成，更详细的关于断点调试、变量观察、GDB、内存视图等功能请自行了解。
