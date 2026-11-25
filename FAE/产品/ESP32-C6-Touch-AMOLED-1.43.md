---
产品: ESP32-C6-Touch-AMOLED-1.43
tags: [FAE, ESP32-C6, AMOLED, ESPHome, HomeAssistant]
---

# ESP32-C6-Touch-AMOLED-1.43

> [!important] ⚠️ 产品参数 / 规格优先
> 参数与兼容性请核对准确型号、PCB/固件版本及条目中的 ⚠️/🔍 标记。客户个案与已验证规格分别记录。

## ⚠️ 产品参数 / 规格（优先查看）

### 能否刷 ESPHome，屏幕触摸能否同时直接使用
- **客户问题/现象**：客户问本板能否刷ESPHome。
- **涉及产品/型号**：ESP32-C6-Touch-AMOLED-1.43；原板资料引用CO5300显示与FT6146触摸。
- **根因**：ESPHome支持C6主控，不代表该整板屏/触摸/音频和电源控制已有完整适配。
- **回复内容（解决方法）**：原答支持自行适配。本次复核[ESPHome ESP32平台](https://esphome.io/components/esp32/)列C6且需esp-idf框架，[mipi_spi显示驱动](https://esphome.io/components/display/mipi_spi/)列CO5300。按实际板图和固件版本配置GPIO/初始化/供电，触摸芯片支持和坐标映射另核对；不要拿S3-1.43/C6-1.32的引脚或YAML通刷。原问答未找到该板完整已验证配置/固件，不能保证刷入后显示/触摸/音频全部即用。[本板资料](https://docs.waveshare.net/ESP32-C6-Touch-AMOLED-1.43/)。
- **相关报错/日志**：🔍 主控/驱动官方支持与整板未验证分开记录；无本次编译/烧录/HA接入实测。来源：2026-11-06《确认 ESPHome 刷写兼容性》，本次完整读取原问答；除另注外未实板复测。

## 其他问题
