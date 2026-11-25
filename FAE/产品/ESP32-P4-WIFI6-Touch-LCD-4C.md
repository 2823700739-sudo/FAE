---
产品: ESP32-P4-WIFI6-Touch-LCD-4C
tags: [FAE, ESP32-P4, LCD, MIPI-DSI, 触摸]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-4C.htm
---

# ESP32-P4-WIFI6-Touch-LCD-4C

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-4C、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-XC/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-XC/Product-Overview.md`。核对日期：2026-10-31。

### LCD 和触摸使用什么控制器
- **客户问题/现象**：外国客户询问显示屏使用什么 controller，并追问 JD9365 信息来源。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-4C。
- **根因**：官方商品页和 README 没有直接给出 LCD 模组 BOM/芯片丝印；JD9365 来自官方 BSP 对 `esp_lcd_jd9365` 的软件依赖。
- **回复内容（解决方法）**：对外严谨表述为：`According to the official BSP, the display is driven using the JD9365-compatible MIPI-DSI driver. The main processor is the ESP32-P4NRW32, and the touch controller is GT9271.` 即主控为 ESP32-P4NRW32，LCD 使用 JD9365 兼容的 2-lane MIPI-DSI 驱动，触摸控制器为 GT9271，并使用 GT911 兼容驱动。
- **相关报错/日志**：🔍 JD9365 可由 BSP 驱动依赖确认，但目前没有商品页 BOM 或实物丝印证据，不宜绝对表述为已确认的屏幕物料型号。
