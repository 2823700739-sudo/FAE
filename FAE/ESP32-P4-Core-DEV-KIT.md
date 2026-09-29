---
产品: ESP32-P4-Core-DEV-KIT
tags: [FAE, ESP32-P4, 摄像头, MIPI-CSI]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Core-DEV-KIT.htm
---

# ESP32-P4-Core-DEV-KIT

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
