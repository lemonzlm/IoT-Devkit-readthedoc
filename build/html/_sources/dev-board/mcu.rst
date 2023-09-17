=========================
MCU 基础
=========================
实验箱设计采用双MCU并行设计，即 **ESP32** 和 **STM32L475**，每个MCU均单独提供WIFI, Bluetooth（重点BLE），RFID, NFC, 十余种外部传感器，LoRa, NB-IoT以及丰富的预留接口。可承担绝大部分IoT节点功能实验。

.. toctree::
    :maxdepth: 3

    ESP32 <esp32.md>
    STM32L475 <stm32l475.md>
