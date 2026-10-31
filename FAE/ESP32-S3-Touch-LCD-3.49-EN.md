---
产品: ESP32-S3-Touch-LCD-3.49-EN
tags: [FAE, ESP32-S3, USB, 串口]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-LCD-3.49-EN.htm
---
运行温度限制：0-65度 不防水
# ESP32-S3-Touch-LCD-3.49-EN

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 能否扩展 GPS 或作为本地算力电脑的机器人终端
- **客户问题/现象**：希望扩GPS；或让单片机调用自配电脑AI做陪伴教育机器人。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN、V1/V2。
- **根因**：有联网音频/触控和扩展焊盘基础，完整业务需软硬件开发，不是板上运行大模型。
- **回复内容（解决方法）**：GPS通常用UART，确认3.3V TTL、供电独立核对、TX/RX交叉共地，按V1/V2空闲GPIO并解析定位协议。机器人可由板采音/播报/表情触控，电脑提供ASR/模型/TTS服务，局域网WebSocket等传输；电机需另加驱动和足额供电。延迟、AEC和打断需联调。
- **相关报错/日志**：⚠️ 2026-10-26/29方案，无具体GPS型号或整机实测。

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

## 其他问题

### 出厂显示正常、自编程序有细白条如何排查
- **客户问题/现象**：照片细亮条，后补准确型号且原厂固件无异常。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN、AXS15231B、QSPI、V1/V2。
- **根因**：⚠️ 尚未确诊；明确QSPI后不再套用先前RGB欠载推断，照片还可能有拍屏纹路。
- **回复内容（解决方法）**：同板同电源比较对应版本官方初始化、QSPI频率/引脚、像素格式/旋转偏移；重点确认DMA完成后才flush_ready与复用缓冲，刷新坐标/行宽/字节长度正确。单任务/正确锁，纯蓝灰静态、关闭音频Wi-Fi动画后逐项加回，降低QSPI频率作定位；固定位置还查排线/屏本体，断电操作。原厂正常缩小范围但非所有工况硬件保证。
- **相关报错/日志**：2026-10-27问答；需框架版本、初始化/刷新代码、肉眼静态是否出现。

### 申请日本技适认证可提供哪些资料，芯片手册能否替代天线资料
- **客户问题/现象**：客户自办技适认证，索要原理图/BOM/PCB、天线及射频参数、专用测试固件和其他认证报告；当前仅能提供PDF原理图，暂无天线认证资料。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN、ESP32-S3、V1/V2。
- **根因**：芯片射频规格不等于板上天线参数、整机实测或认证文件；“暂无天线认证资料”也不能扩大为“整机没有任何认证”。
- **回复内容（解决方法）**：按已确认范围答复：可提供与送检PCB版次对应的PDF原理图，BOM/PCB源文件当前不提供；天线型号/增益/方向图、整机发射参数能否补充需另行核实。测试固件/操作说明及其他地区证书不要先承诺，应让客户提供认证机构要求的测试模式、频率、功率和文件清单，再评估配合。ESP32-S3手册第6章可查芯片Wi-Fi/BLE标准、频率及发射功率，不能替代天线或整机报告。[芯片手册](https://documentation.espressif.com/esp32-s3_datasheet_cn.pdf)、[版本资料](https://docs.waveshare.net/ESP32-S3-Touch-LCD-3.49/Resources-And-Documents/)。
- **相关报错/日志**：🔍 资料提供范围为2026-10-22当时确认状态，不是未来承诺或认证通过保证。来源：“回复技适认证资料咨询”。
