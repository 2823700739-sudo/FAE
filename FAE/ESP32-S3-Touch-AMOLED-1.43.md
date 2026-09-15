---
产品: ESP32-S3-Touch-AMOLED-1.43
tags: [FAE, ESP32-S3, AMOLED, 显示, 烧录]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-AMOLED-1.43.htm
---

# ESP32-S3-Touch-AMOLED-1.43

### 为什么官方例程达不到 16.7M 色
- **客户问题/现象**：产品标称16.7M色，但实际例程显示色深较低。
- **涉及产品/型号**：ESP32-S3-Touch-AMOLED-1.43、CO5300/SH8601。
- **根因**：屏幕控制器硬件可支持RGB888，但官方Arduino/ESP-IDF例程默认LVGL为RGB565 16bit，只能表示65,536色。
- **回复内容（解决方法）**：要真正输出16.7M色，不能只把 `LV_COLOR_DEPTH`改成32；还需改控制器像素格式、QSPI写屏为每像素3字节、flush回调和资源格式。完整帧传输量增加约50%，刷新率会下降。结论是硬件可行、现成例程未实现。
- **相关报错/日志**：当前批次可能为CO5300，旧批次可能为SH8601，修改前先确认驱动IC。

### 烧录出厂固件成功但重新上电仍黑屏
- **客户问题/现象**：烧录工具显示FINISH、地址看似0x0，但AMOLED全黑。
- **涉及产品/型号**：ESP32-S3-Touch-AMOLED-1.43。
- **根因**：优先怀疑SH8601/CO5300批次与固件不匹配，其次是把app.bin当完整合并固件、残留数据、下载模式、供电或硬件。
- **回复内容（解决方法）**：核对完整型号、屏幕驱动批次和factory/merged BIN；完整擦除后将合并固件烧到0x0。记录上电最初20～30行日志：下载等待查BOOT，invalid header查BIN/地址，Brownout查供电；程序正常但仍黑时再查GPIO42使能、GPIO21复位、排线、连接器和屏幕供电。
- **相关报错/日志**：`waiting for download`、`invalid header`、`Brownout`。

