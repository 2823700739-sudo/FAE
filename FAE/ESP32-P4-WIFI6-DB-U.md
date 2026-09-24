---
产品: ESP32-P4-WIFI6-DB-U
aliases: [ESP32-P4-WIFI6-DB]
tags: [FAE, ESP32-P4, USB Host, USB Type-C, VBUS]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-DB.htm
---

# ESP32-P4-WIFI6-DB-U

### 4PIN USB OTG 转 Type-C Host 的供电与保护
- **客户问题/现象**：计划使用 4PIN USB OTG 2.0 High-Speed 接口接 USB Device，自制 Type-C Host 母座转接板，询问针序、VBUS 输出电流、限流/过流/防反灌及 CC 电路。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB-U、ESP32-P4、USB Host、USB Type-C。
- **根因**：官方原理图 P1 为 MX1.25 4pin：1=`VCC_5V`、2=`USBD_N`、3=`USBD_P`、4=GND。`VCC_5V` 是整板 5V 电源网，经 Q2 AO3401 等由 Type-C UART 口的 `USB0_5V` 输入；P1 处未见独立 USB VBUS 限流开关、过流检测或反向阻断器件。原理图中 MP1658 的 `3A MAX` 是 3.3V 降压输出标注，不能当作 P1 VBUS 额定电流。
- **回复内容（解决方法）**：ESP32-P4 可在该高速 OTG 数据口运行 USB Host，微雪官方有 `08_usb_host_msc` 示例；仍需对应 USB Host 类驱动。板卡由稳定 5V 供电时，P1-1 可取得约 5V，但供电依赖上游电源、板载负载、布线和接插件；⚠️ 微雪未给该接口独立最大持续输出电流，不承诺 500mA、1.5A 或 3A。自制 Type-C Source 口除 CC1/CC2 各自配置正确的 Rp（按实际可供电流宣告）外，还应使 VBUS 在检测到 Sink 后经限流/短路保护、可关断、反向阻断的高边电源开关输出，并考虑 ESD、输入电源预算和 USB 2.0 D+/D- 双面触点并接。只需 5V USB 2.0 Host 时通常不必配置 PD。勿将 Type-C VBUS 直接无保护地接到 P1 的 `VCC_5V`；先空载测 5V，再限流负载测试、观察压降与热量，最后测试枚举及插拔。🔍 若需确定某硬件批次实际连续输出电流或已有保护能力，以对应版本原理图、器件料号及实测为准。
- **相关报错/日志**：⚠️ P1 端无独立电流额定值和专用过流保护；`3A MAX` 不是该接口的输出能力。参考：https://github.com/waveshareteam/ESP32-P4-WIFI6-DB/blob/main/hardware/ESP32-P4-WIFI6-DB.pdf
