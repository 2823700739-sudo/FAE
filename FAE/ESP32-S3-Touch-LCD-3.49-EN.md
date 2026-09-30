---
产品: ESP32-S3-Touch-LCD-3.49-EN
tags: [FAE, ESP32-S3, USB, 串口]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-LCD-3.49-EN.htm
---
运行温度限制：0-65度 不防水
# ESP32-S3-Touch-LCD-3.49-EN

### Type-C 使用什么串口芯片和驱动
- **客户问题/现象**：询问是否使用CH340/CH343/CP2102。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN。
- **根因**：USB D-/D+经电阻直接连接ESP32-S3 GPIO19/20，没有独立USB转串口芯片。
- **回复内容（解决方法）**：使用ESP32-S3内置USB Serial/JTAG（USB CDC）。Windows 10/11通常自动识别为USB串行设备；异常时安装Espressif USB Serial/JTAG驱动，不要安装CH343驱动。
- **相关报错/日志**：无。

### 供电电压、电源容量与实际工作电流
- **客户问题/现象**：询问“电压电流要求是？”。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN（A 款不带电池版本）。
- **根因**：USB Type-C 输入、板上 3.3V 电源与整机实际耗电属于不同指标；耗电随背光、Wi-Fi、音频及外接负载变化。🔍 本次查阅的官方资料未提供整机典型/峰值电流及测试条件。
- **回复内容（解决方法）**：通过 USB Type-C 使用稳定的 5V 电源。工程选型建议采用 5V/2A 电源以预留余量，此为建议电源容量，并非官方最低要求或整机恒定耗电。实际工作电流应在目标固件、背光亮度、Wi-Fi 收发、音频音量和外接负载条件下测量，不能直接套用 ESP32-S3 芯片电流或稳压器最大输出值。依据：[官方原理图](https://files.waveshare.com/wiki/ESP32-S3-Touch-LCD-3.49/ESP32-S3-Touch-LCD-3.49-Schematic.pdf) 第 1 页 Type-C Interface / POWER（V1）；[官方版本资料](https://docs.waveshare.net/ESP32-S3-Touch-LCD-3.49/Resources-And-Documents/) 提供 V1/V2 对应原理图；[产品介绍](https://docs.waveshare.net/ESP32-S3-Touch-LCD-3.49/) 标明 EN 为不带电池版本。V2 充电电流有调整，不将 V1 充电参数套用到 V2。
- **相关报错/日志**：无；🔍 未进行整机电流实测。

### 是否支持 USB PD 快充协议及 PD 充电器供电
- **客户问题/现象**：询问“支持 pd 协议吗”；客户已澄清为 USB PD，而非 DHCP。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN、USB Type-C、V1/V2。
- **根因**：根据官方 V1/V2 原理图，Type-C 的 CC1/CC2 分别经 5.1kΩ 电阻下拉到 GND，仅用于受电端连接识别，没有接入 PD 协商控制器。V1 为 R9/R14，V2（图纸标注 V1.1）为 R11/R16。
- **回复内容（解决方法）**：依据电路判断，不支持 USB PD 快充协商，不能请求 9V、12V、20V 等电压；应使用 5V 输入。符合 USB-C 规范、具有默认 5V 输出的 PD 充电器通常可以给该板供电，但仍按 5V 工作，不会启用 PD 高压快充。不要使用诱骗器将高于 5V 的电压送入板上 Type-C。实际电源选型需按 5V 档容量和整机负载核验，不能用充电器最高 PD 功率推算板子可用功率。依据：[V1 原理图](https://files.waveshare.com/wiki/ESP32-S3-Touch-LCD-3.49/ESP32-S3-Touch-LCD-3.49-Schematic.pdf)、[官方 V2 原理图](https://github.com/waveshareteam/ESP32-S3-Touch-LCD-3.49-V2/blob/main/schematic/ESP32-S3-Touch-LCD-3.49%20V2.pdf) 第 1 页 Type-C Interface；[乐鑫 USB Type-C 硬件设计指南](https://docs.espressif.com/projects/esp-iot-solution/en/latest/usb/usb_overview/usb_typec_hardware_guide.html) 的 USB Type-C Role Identification and Power Detection / USB PD Power Negotiation Process。
- **相关报错/日志**：无；🔍 已核对公开原理图，未对具体充电器、线材组合进行实物测试。
