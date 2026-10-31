---
产品: ESP32-P4-Core-DEV-KIT
tags: [FAE, ESP32-P4, 摄像头, MIPI-CSI]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Core-DEV-KIT.htm
---

# ESP32-P4-Core-DEV-KIT

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 是否板载 C6 或 C5，能否直接使用无线协处理器通信
- **客户问题/现象**：确认该产品是否也有P4与C6/C5的板内通信通道。
- **涉及产品/型号**：ESP32-P4-Core-DEV-KIT。
- **根因**：本型号未板载ESP32-C6/C5；不能把其他P4-WIFI6或Module型号的SDIO无线架构直接套用。
- **回复内容（解决方法）**：P4本身无Wi-Fi/Bluetooth射频，因此本板没有可直接初始化的板载C6/C5 Hosted无线链路。Core模组与含C6的ESP32-P4-Module不是同一产品。若外接C6/C5，需自行设计供电、接口、复位和匹配的主从固件，现有其他板引脚/配置不能照搬；未确认具体外接方案。
- **相关报错/日志**：🔍 依据本地Docs_zh `ESP32-P4-Core-DEV-KIT/Product-Overview.md`的型号范围和板载资源核对，未做外接适配。[官方资料](https://docs.waveshare.net/ESP32-P4-Core-DEV-KIT/)。

### CSI 接口为何没有 MCLK 和 RST，如何接 OV5647
- **客户问题/现象**：摄像头接口未标PMCLK/RST，确认主板为Core-DEV-KIT，准备接RPi Camera (B) OV5647。
- **涉及产品/型号**：ESP32-P4-Core-DEV-KIT、RPi Camera (B)、OV5647、22Pin CSI。
- **根因**：CSI_CLK_P/N是摄像头输出的差分传输时钟，不是Sensor输入MCLK/XCLK。图纸CSI_IO0第6脚经R42=10kΩ上拉3.3V，CSI_IO1第5脚R43不装，两线均未接P4 GPIO，原板不能GPIO硬复位。
- **回复内容（解决方法）**：需摄像头模组自带时钟并满足上电/复位条件；指定RPi Camera (B)带本地时钟电路，OV5647驱动可经SCCB写0x0103软复位，正常使用不要求再飞MCLK/RST。板端22Pin/0.5mm、相机端15Pin/1.0mm，用对应22→15摄像头排线，断电核对触点方向。其他裸Sensor/模组不能仅凭OV5647型号套用。[Core资料](https://docs.waveshare.net/ESP32-P4-Core-DEV-KIT/)、[相机实物图](https://www.waveshare.net/photo/accBoard/RPi-Camera-B/RPi-Camera-B-2.jpg)、[OV5647驱动](https://github.com/espressif/esp-video-components/blob/master/esp_cam_sensor/sensors/ov5647/ov5647.c)。
- **相关报错/日志**：🔍 依据图纸和指定模组资料，未做客户组合实板测试。来源：2026-10-21“查找 sensor 接口缺少 PMCLK 和 RST 的原因”。

### 连接 IMX708 应购买哪种排线
- **客户问题/现象**：确认 Core 板的 22Pin CSI 是否兼容 Raspberry Pi Camera Module 3 / IMX708。
- **涉及产品/型号**：ESP32-P4-Core-DEV-KIT、IMX708。
- **根因**：板端为 22Pin、0.5 mm、Pi 5 mini CSI；Camera Module 3 端为 15Pin、1.0 mm。
- **回复内容（解决方法）**：购买 Raspberry Pi 5 Standard-to-Mini Camera Cable，即 22Pin 转 15Pin、0.5 mm → 1.0 mm。除非摄像头模组本身也是 22Pin，否则不要买 22Pin→22Pin。插线时以连接器触点方向为准。软件仍需自行适配第三方 IMX708/DW9807 驱动。
- **相关报错/日志**：无。

### 两个 Type-C 是否分别用于供电和 OTG
- **客户问题/现象**：询问板上两个 Type-C 是否一个用于供电、另一个用于 USB OTG。
- **涉及产品/型号**：ESP32-P4-Core-DEV-KIT。
- **根因**：两个 Type-C 都可供电和烧录；标为 UART 的 Type-C 还经串口芯片用于日志调试。USB OTG 2.0 HS 单独由 MX1.25 4Pin 接口引出，而非这两个 Type-C 口之一。
- **回复内容（解决方法）**：日常烧录和查看日志建议接 Type-C UART；需要连接 USB OTG 设备时使用 MX1.25 4Pin 接口及正确转接线，同时按 Host/Device 用途处理 VBUS 和供电，不要直接把任一 Type-C 当作 OTG Host 口。[官方硬件说明](https://docs.waveshare.net/ESP32-P4-Core-DEV-KIT/)。
- **相关报错/日志**：无。
