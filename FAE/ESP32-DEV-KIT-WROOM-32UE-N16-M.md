---
产品: ESP32-DEV-KIT-WROOM-32UE-N16-M
tags: [FAE, ESP32, Wi-Fi, 功耗]
商品链接: https://www.waveshare.net/shop/ESP32-DEV-KIT-WROOM-32UE-N16-M.htm
---

# ESP32-DEV-KIT-WROOM-32UE-N16-M

### Wi-Fi 发射和接收电流是多少，数据从哪里来
- **客户问题/现象**：询问该开发板发射/接收电流及数值出处。
- **涉及产品/型号**：ESP32-DEV-KIT-WROOM-32UE-N16-M、ESP32-WROOM-32UE。
- **根因**：乐鑫模组数据手册第 6.4 节表 16 给出不同 Wi-Fi 模式的模组端 3.3V 电流；它不是整块微雪开发板的实测输入电流，射频功率、模式和占空比会改变读数。
- **回复内容（解决方法）**：按所查手册版本，在 25℃、3.3V 条件下，Wi-Fi TX 平均约 165～239mA、峰值约 211～379mA；RX 约 112～118mA。最大值对应 802.11b、19.5dBm TX，平均约 239mA、峰值约 379mA。给模组的 3.3V 电源应留足至少约 500mA 能力；整板若经 USB 5V 供电，可先选 5V/1A 或更高余量电源，但这是供电建议，不是官方整板电流规格。实际整板和应用功耗需在相应工作模式实测。[乐鑫模组数据手册](https://documentation.espressif.com/esp32-wroom-32e_esp32-wroom-32ue_datasheet_en.html)、[微雪板卡文档](https://docs.waveshare.net/ESP32-DEV-KIT-XX/)。
- **相关报错/日志**：⚠️ 数值来自当时所查模组数据手册，未对该开发板实测；若表格版本修订应重新核对。
