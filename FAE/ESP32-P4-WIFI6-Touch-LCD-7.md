---
产品: ESP32-P4-WIFI6-Touch-LCD-7
aliases: [ESP32-P4-WIFI6-Touch-LCD-X]
tags: [FAE, ESP32-P4, USB, 供电, JTAG]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-7.htm
---

# ESP32-P4-WIFI6-Touch-LCD-7

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-7、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-X/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-X/Product-Overview.md`。核对日期：2026-10-31。

### 屏幕刷新率是多少
- **客户问题/现象**：询问720×1280屏幕的刷新率规格。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-7、MIPI-DSI、官方BSP。
- **根因**：官方商品页未直接标注刷新率；按当前官方BSP中的像素时钟、有效区和水平/垂直消隐时序计算约57.6Hz，通常归入60Hz档。
- **回复内容（解决方法）**：对外可回复“屏幕时序约60Hz，按官方BSP计算约57.6Hz”。注意屏幕链路刷新率不等于应用实际帧率；LVGL渲染、缓冲方式、PSRAM带宽及业务负载可能使界面帧率更低。
- **相关报错/日志**：🔍 57.6Hz基于当前官方BSP时序计算，后续BSP或屏幕批次变化时应重新核对。

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
