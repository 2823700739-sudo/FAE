---
产品: T113 Linux LVDS 替代选型
tags: [FAE, 选型, T113, Linux, LVDS, Raspberry-Pi]
---

# T113 Linux LVDS 替代选型

### T113 Linux 加 LVDS 触摸屏平台如何平替
- **客户问题/现象**：现有方案使用T113核心板、LVDS触摸屏、百兆网口和若干IO开关，希望寻找可平替开发板，并询问微雪ESP32-P4或树莓派方案是否合适。
- **涉及产品/型号**：T113核心板、ESP32-P4、Raspberry Pi CM4、CM4-IO-BASE-B、LVDS触摸屏、飞凌OK113i-S/FET113i-S。
- **根因**：直接平替不仅取决于算力和网口，还要同时匹配操作系统、显示物理接口、触摸、背光和IO电气规格。ESP32-P4常规运行ESP-IDF/FreeRTOS且显示接口为MIPI-DSI，不是Linux+LVDS方案；CM4可运行Linux，但CM4-IO-BASE-B提供DSI/HDMI而非直连LVDS。
- **回复内容（解决方法）**：若限定微雪产品，树莓派CM4加CM4-IO-BASE-B比P4更接近原有Linux、网络和GPIO需求，但保留原LVDS屏需增加与屏型号匹配的DSI/HDMI转LVDS板，并单独核对触摸与背光。若要求尽量直接沿用T113和LVDS，可评估飞凌OK113i-S/FET113i-S等同平台方案，但现有镜像仍不能直接通用。选型前必须确认屏幕型号、分辨率、单/双通道LVDS、排线脚位、触摸类型、背光供电，以及IO数量、电压和驱动方式。
- **相关报错/日志**：🔍 未提供现用核心板与屏幕具体型号，不能承诺整板、排线或软件直接替换。
