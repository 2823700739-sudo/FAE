---
产品: ESP32-S3-LCD-3.16
tags: [FAE, ESP32-S3, LCD, 音频, UART]
商品链接: https://www.waveshare.net/shop/ESP32-S3-LCD-3.16.htm
---

# ESP32-S3-LCD-3.16

### 有哪些外部接口，能否连接扬声器和麦克风
- **客户问题/现象**：询问板卡接口及扬声器、麦克风扩展能力。
- **涉及产品/型号**：ESP32-S3-LCD-3.16。
- **根因**：板上没有音频 Codec、功放、扬声器或麦克风，也没有专用 I2S 插座；可用 GPIO 数量和现有外设复用关系较紧张。
- **回复内容（解决方法）**：主要接口包括 Type-C、MX1.25 4Pin USB、SH1.0 UART（GPIO43/44）、SH1.0 I2C（GPIO15/7，与 RTC/IMU 共用）、Micro SD、电池口、RTC 电池口和 IPEX1 天线接口。扬声器需外接 MAX98357A 等 I2S 功放，麦克风可用 INMP441 等 I2S 数字麦克风；无源喇叭不能直接接 GPIO。全双工通常至少需要 BCLK、WS、DIN、DOUT，需复用接口或飞线，可能占用 UART 或影响 RTC/IMU，因此不适合作为可靠语音交互项目的首选。简单提示音可用有源蜂鸣器加驱动管。
- **相关报错/日志**：无。

### 能看到启动日志但程序无法通过 UART 收发
- **客户问题/现象**：USB 转 TTL 接板载 UART 后能收到启动信息，但应用程序不能正常收发数据。
- **涉及产品/型号**：ESP32-S3-LCD-3.16、UART0。
- **根因**：启动日志固定从 UART0 输出，只能证明板子 TX 到电脑 RX 通道正常；应用可能使用了 USB CDC 的 `Serial`，或电脑到板子 RX 方向接线、波特率不正确。
- **回复内容（解决方法）**：板端 RXD/GPIO44 接转换器 TXD，板端 TXD/GPIO43 接转换器 RXD，并可靠共地；使用 3.3V TTL，通常不接转换器 3V3，开发板继续由 Type-C 供电。Arduino 启用 `USB CDC On Boot` 时，硬件 UART0 应使用 `Serial0.begin(115200, SERIAL_8N1, 44, 43)`。电脑设置 115200、8N1、无流控。能看到程序发送的 `UART0 ready` 但无回显时，重点检查 TTL TXD 到板端 GPIO44；必要时先短接转换器 TX/RX 做回环测试。
- **相关报错/日志**：只能看到启动信息而看不到应用输出，优先检查 `Serial` 与 `Serial0` 混用及应用波特率。
