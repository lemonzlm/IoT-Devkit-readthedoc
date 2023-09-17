# 开发环境

开发环境需要确定 IDE 的操作系统，采用的编程语言，集成开发环境 IDE，以及 SDK。这主要取决于所采用的：

- 编程语言
- MCU 可使用的 IDE 环境
- 官方的 SDK
- IDE 对本实验箱的兼容性
- IDE 的跨平台特性以及易用性

## 1 OS

本手册的实验项目采用**Ubuntu20**，但本项目的 IDE 是可以跨平台使用的，因此，在 Windows，Mac OS X 上同样可以使用。区别仅在于驱动以及 python 的版本。

但在实际应用中，跨平台项目，IDE 以及开发语言（如 Python）在不同的 OS 中往往呈现不兼容的情况，考虑到嵌入式开发，开源项目，以及 GitHub 上大量的代码均是在 Linux 平台下开发和测试，因此，本文档和实验系统均已 Linux 为主，同学们可以在自己的电脑上使用 Windows，允许但不提倡，建议同学自己的机器可以采用虚拟机的方式。

## 2 开发语言

本系统的两块 MCU 均支持开发语言与开发框架。

- C
- [micropython](https://micropython.org)
- [arduino](https://www.arduino.cc/en/software)
- [platformio](https://platformio.org)

MicroPython 以及 Arduino 均具有简单易学的特点，同时也是跨平台语言，在构建项目原型以及验证设计方法时有着方便快捷的特点，但由于以下两个原因，本手册不以这两种语言讲述，建议有兴趣的同学自行学习使用。

- 运行速度和功耗不适宜生产环境。
- 不能使用 OS，无法完全隔离硬件。

本手册中以生产环境中的默认开发语言 C 为基础叙述，这也是两款 MCU 的官方指定开发语言。

## 3 IDE

本手册的实验箱涉及到两颗 MCU，因此 IDE 的选择应当兼顾到两块 MCU 的兼容性。IDE 有官方提供的 IDE，以及第三方 IDE。

### 3.1 官方 IDE

对于 ESP32:

- Eclipse + [esp-idf plugin](https://github.com/espressif/idf-eclipse-plugin/blob/master/README.md)
- VsCode + [esp-idf plugin](https://github.com/espressif/vscode-esp-idf-extension/blob/master/docs/tutorial/install.md)

对于 STM32L475

- STM32CubeIDE

官方 IDE 的共同特点是支持比较丰富。但是其缺点是都基于 eclipse，Eclipse 的最大不足有两点：

- 由于需要 Java 虚拟机，运行速度慢，对机器要求高
- 代码补全功能差，编程不友好。

因此，本手册不采用官方提供的 IDE。

### 3.2 第三方 IDE

对于 ESP32:

- Clion
- vscode + platformio

对于 STM32L475:

- Clion + stm32cubemx
- vscode + platformio
- keil
- IAR

本项目希望两块 MCU 能够统一 IDE，因此，由于 keil，iar 均只用于 Windows，不予考虑。可见，vscode + platformio 是最统一的，且是开源的，但是经过实测，其稳定性和对官方的兼容性都存在一定问题，因此，也不予采用。

Clion 是 jetbrain 旗下的一款针对 c/c++的 ide 平台，近年增加了对嵌入式开发的支持，特别是内置支持 STM32，考虑到 Clion 的优异性能对嵌入式的支持，**本手册采用 Clion 作为统一开发平台。**

特别的，clion 是跨平台 IDE，因此他并不限于在 Linux 系统下开发。

## 4 SDK 以及 Tools

### STM32L4CUBE

待更新

### ESP-IDF

见后节
