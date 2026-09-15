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

