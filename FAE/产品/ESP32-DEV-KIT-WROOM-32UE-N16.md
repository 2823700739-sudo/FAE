---
产品: ESP32-DEV-KIT-WROOM-32UE-N16
tags: [FAE, ESP32, 客户问题]
---

# ESP32-DEV-KIT-WROOM-32UE-N16

> [!important] ⚠️ 产品参数 / 规格优先
> 参数按准确型号、硬件和软件版本使用；🔍 表示尚需资料或实板验证，⚠️ 表示问题尚未确诊或闭环。

## ⚠️ 产品参数 / 规格（优先查看）

### 能否搭建面包板版小智，需要哪些外围
- **客户问题/现象**：希望使用该板搭建面包板小智。
- **涉及产品/型号**：ESP32-DEV-KIT-WROOM-32UE-N16。
- **根因**：ESP32-WROOM-32UE-N16为原版ESP32、16MB Flash、无PSRAM；小智有bread-compact-esp32配置，但板本体不等于完整音频终端。
- **回复内容（解决方法）**：选ESP32目标的bread-compact-esp32，并按OLED分辨率选配置；按[config.h](https://github.com/78/xiaozhi-esp32/blob/main/main/boards/bread-compact-esp32/config.h)外接I2S麦克风、I2S功放/喇叭和对应OLED。先核对GPIO与供电，不能烧ESP32-S3的面包板BIN。[官方配置](https://github.com/78/xiaozhi-esp32/blob/main/main/boards/bread-compact-esp32/config.json)。此处准确型号不带-M，不凭名字相近合并到另一带-M产品的电流记录。
- **相关报错/日志**：🔍 芯片规格及适配配置判断可行，未在这块微雪板上实测。 来源：2026-11-19《确认面包板小智兼容性》。
