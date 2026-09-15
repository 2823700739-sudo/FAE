---
产品: ESP32通用
tags: [FAE, ESP32, LVGL, Arduino, ESP-IDF]
---

# ESP32 通用问题

### LVGL 画面撕裂，是否因为 XIP 没开
- **客户问题/现象**：Arduino/LVGL界面动画时撕裂、闪屏。
- **涉及产品/型号**：未明确；ESP32 RGB/SPI/QSPI显示方案通用。
- **根因**：XIP通常不是直接根因；更常见是单framebuffer同时读写、DMA未完成就复用缓冲、PSRAM/EDMA带宽不足、TE/VSYNC不同步或RGB时序错误。
- **回复内容（解决方法）**：RGB屏先查两个完整framebuffer与VSYNC换帧，降低PCLK 20%～40%验证带宽；SPI/QSPI异步DMA必须在完成回调后调用 `lv_display_flush_ready()`，有TE脚时同步刷新。只有问题主要发生在NVS/文件系统/OTA写Flash期间，且使用RGB+PSRAM bounce buffer时，才重点测试PSRAM XIP。
- **相关报错/日志**：LVGL双draw buffer不等于LCD双framebuffer；Arduino普通 `.ino` 中定义宏不能自动重编预编译SDK配置。

### 构建报编码问题或旧 build 缓存
- **客户问题/现象**：截图显示构建异常，历史答复判断为编码和旧build目录问题。
- **涉及产品/型号**：未明确，目标为ESP32-S3。
- **根因**：⚠️ 原始错误文本未保留在聊天正文，仅有截图，不能进一步确认。
- **回复内容（解决方法）**：手动删除项目 `build`，PowerShell执行 `chcp 65001`、设置 `PYTHONUTF8=1`，再 `idf.py set-target esp32s3` 和 `idf.py build`。若仍失败应补充可复制的完整日志。
- **相关报错/日志**：原始日志缺失。

### Arduino 上传时报 No serial data received
- **客户问题/现象**：工程编译成功，但上传时无法连接芯片。
- **涉及产品/型号**：ESP32-P4系列，具体板型未明确。
- **根因**：芯片未进入下载模式、COM被占用、选错串口、线材或USB口异常；与应用代码内容无关。
- **回复内容（解决方法）**：关闭串口监视器，按住BOOT、短按RST后上传，出现 `Connecting...` 再松开BOOT；重新确认拔插后新增COM，换数据线并直连电脑，必要时将Upload Speed降到115200。设备管理器无新增串口时先处理驱动和线材。
- **相关报错/日志**：`Failed to connect to ESP32-P4: No serial data received`、`Failed uploading: exit status 2`。

