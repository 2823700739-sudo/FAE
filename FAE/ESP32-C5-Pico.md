---
产品: ESP32-C5-Pico
tags: [FAE, ESP32-C5, WiFi6, TTS]
商品链接: https://www.waveshare.net/shop/ESP32-C5-Pico.htm
---

# ESP32-C5-Pico

### 与 ESP32-C5-MINI-KIT 有什么区别
- **客户问题/现象**：比较两块板性能、尺寸与扩展。
- **涉及产品/型号**：ESP32-C5-Pico、ESP32-C5-MINI-KIT。
- **根因**：两者主控、4MB Flash和无线能力基本相同，差别主要是板型和接口。
- **回复内容（解决方法）**：Pico采用Raspberry Pi Pico板型，带GH1.25电池座和TCA9554 IO扩展，适合面包板、更多外设和Pico扩展板；MINI-KIT更小，适合焊接集成。两者引脚不同，移植程序要改GPIO。
- **相关报错/日志**：无。

### 是否支持中文离线 TTS
- **客户问题/现象**：希望无需联网把文本合成为语音。
- **涉及产品/型号**：ESP32-C5-Pico。
- **根因**：ESP-SR有C5离线中文TTS，但板卡仅4MB Flash、384KB SRAM且无PSRAM，资源余量很紧；也没有板载扬声器/功放。
- **回复内容（解决方法）**：能做但不推荐完整语音项目。需外接I2S DAC/功放（如MAX98357A）与喇叭；少量固定提示语优先存WAV/MP3。需要更多功能、语音识别或稳定余量时改用8/16MB Flash并带PSRAM的ESP32-S3。
- **相关报错/日志**：⚠️ 历史无该型号完整TTS实测记录。

