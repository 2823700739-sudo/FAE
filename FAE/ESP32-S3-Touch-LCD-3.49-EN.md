---
产品: ESP32-S3-Touch-LCD-3.49-EN
tags: [FAE, ESP32-S3, USB, 串口]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-LCD-3.49-EN.htm
---

# ESP32-S3-Touch-LCD-3.49-EN

### Type-C 使用什么串口芯片和驱动
- **客户问题/现象**：询问是否使用CH340/CH343/CP2102。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49-EN。
- **根因**：USB D-/D+经电阻直接连接ESP32-S3 GPIO19/20，没有独立USB转串口芯片。
- **回复内容（解决方法）**：使用ESP32-S3内置USB Serial/JTAG（USB CDC）。Windows 10/11通常自动识别为USB串行设备；异常时安装Espressif USB Serial/JTAG驱动，不要安装CH343驱动。
- **相关报错/日志**：无。

