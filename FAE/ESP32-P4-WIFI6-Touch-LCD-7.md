---
产品: ESP32-P4-WIFI6-Touch-LCD-7
aliases: [ESP32-P4-WIFI6-Touch-LCD-X]
tags: [FAE, ESP32-P4, USB, 供电, JTAG]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-7.htm
---

# ESP32-P4-WIFI6-Touch-LCD-7

### USB TO UART 供电时 OTG 口为什么没有 5V
- **客户问题/现象**：给一个 Type-C 口供电时，只有该口和排针 5V 有电，另一个 OTG 口仍没有电压；进一步询问电池供电时能否使用 OTG Host。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-7。
- **根因**：两个 USB 口的 VBUS 不直接并联，USB0_5V、USB1_5V 经 MOS 管进入板内 5V 电源；电池供电路径没有把电池升压后回送到 `VCC_5V` 或 OTG VBUS。
- **回复内容（解决方法）**：从 USB TO UART 供电时，排针 5V 有电而 OTG VBUS 为 0V 属正常，板子不会把 UART 口的 5V 反向送到 OTG。仅用电池时排针 5V 与 OTG VBUS 同样没有 5V，因此 OTG 口缺少标准 USB Host 应提供的 VBUS，普通 U 盘、摄像头等通常不能启动或枚举。电池状态下需要 USB Host 时，优先使用带独立供电并隔离上行 VBUS 的 Hub；或另设“电池→5V升压→限流高边开关→OTG VBUS”电路并可靠防反灌。不要直接把排针 5V 或升压输出随意并到 Type-C VBUS。若直接从 OTG 输入 5V 后整板仍不启动，再检查 Q3、U11 与具体硬件版本。
- **相关报错/日志**：此处“GPIO 有 5V”实际指排针 5V 电源脚；普通 GPIO 逻辑电平仍为 3.3V。

### USB TO UART 和 OTG 口能否直接用作 JTAG
- **客户问题/现象**：询问能否从板载 Type-C 口进行 JTAG 调试或烧录。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-7。
- **根因**：USB TO UART 口连接 CH343P，只提供串口；OTG Type-C 走 USB 2.0 HS，也不是 P4 内置 USB-Serial/JTAG 通道。
- **回复内容（解决方法）**：需要使用 P4 内置 USB-JTAG 时，从 40Pin 排针引出 GPIO24/D-、GPIO25/D+、GND，并连接合适的 USB 转接线；OpenOCD 使用 `board/esp32p4-builtin.cfg`。常规烧录继续使用 USB TO UART。不要为了外置 GPIO-JTAG 随意烧写不可逆 eFuse。
- **相关报错/日志**：无。
