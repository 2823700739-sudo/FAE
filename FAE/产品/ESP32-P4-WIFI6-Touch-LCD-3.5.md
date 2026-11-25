---
产品: ESP32-P4-WIFI6-Touch-LCD-3.5
tags: [FAE, ESP32-P4, 视频]
---

# ESP32-P4-WIFI6-Touch-LCD-3.5

> [!important] ⚠️ 产品参数 / 规格优先
> 参数与兼容性按准确型号和硬件/固件版本确认；保留未实测及待核实标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-3.5、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-3.5/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-3.5/Product-Overview.md`。核对日期：2026-10-31。

### 能否播放几十分钟或数小时的长视频
- **客户问题/现象**：询问能否播放长视频。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-3.5。
- **根因**：播放时长与编码格式、文件大小、TF和播放器稳定性共同相关，存在播放器例程不等于任意MP4兼容。
- **回复内容（解决方法）**：按当时官方TF MP4例程转码为320×480、17fps、MJPEG视频＋AAC音频。几十分钟/数小时需实际连续播放测试，资料未给时长保证；先区分本地TF或网络源。
- **相关报错/日志**：⚠️ 2026-10-26资料核对，无长时间实测。[播放说明](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-3.5/ESP-IDF/#10-mp4-player)。
