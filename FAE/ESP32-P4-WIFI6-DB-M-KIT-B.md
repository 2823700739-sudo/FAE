---
产品: ESP32-P4-WIFI6-DB-M-KIT-B
aliases: [ESP32-P4-WIFI6-DB]
tags: [FAE, ESP32-P4, ESP32-C5, 摄像头, Wi-Fi, OTA]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-DB-M-KIT-B.htm
---

# ESP32-P4-WIFI6-DB-M-KIT-B

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 能否通过双频 Wi-Fi 把摄像头视频传到手机并支持 OTA
- **客户问题/现象**：希望由 P4 采集摄像头数据，经 2.4/5GHz Wi-Fi 上传到手机应用，并为后续 P4 与 C5 固件提供 OTA；另问“Wi-Fi传输清晰度多少、ipcamera能到多少分辨率”。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB-M-KIT-B、ESP32-P4、ESP32-C5、OV5647。
- **根因**：P4 提供 2-lane MIPI-CSI、ISP 和 H.264/JPEG 硬件能力，板载 C5 通过 SDIO/ESP-Hosted 为 P4 提供 2.4GHz/5GHz Wi-Fi 6。该硬件组合具备实现条件，但官方摄像头和网络示例是分开的，不是烧录即用的完整手机视频方案；P4 与 C5 也是两个独立固件目标。
- **回复内容（解决方法）**：建议先购买一套做 PoC：使用 OV5647、720p 15～30fps、H.264 单路上行，手机与开发板先连接同一路由器并用 RTSP 验证，再扩展 WebSocket/WebRTC、公网鉴权等。也可使用 SoftAP 让手机直连；C5 不支持 Wi-Fi Direct/P2P。“双频”表示可选择 2.4GHz 或 5GHz，并非同时双频工作。P4 使用 HTTPS OTA、双 OTA 分区、启动确认和自动回滚；C5 的 ESP-Hosted 固件需单独 Slave OTA，并保留 C5 UART 作为救砖通道。嵌入式端需要整合采集、编码、缓存和网络发送，手机软件负责连接、解码和显示。
  2026-10-22补充：1920×1080（约200万像素）、H.264 1080p@30fps属于采集/编码能力，不等于无线端到端指标；清晰度还受镜头对焦和码率影响。若ipcamera指具体例程，先取代码链接/压缩包核对分辨率、帧率和码率，不能仅凭程序名称承诺1080p无线效果。[DB资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DB/)。
  通信架构补充（2026-10-31）：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C5 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C5 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C5 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C5 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C5 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C5 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C5。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 本型号文档列出的P4侧信号为CLK=GPIO18、CMD=GPIO19、D0～D3=GPIO14～17、从机RESET=GPIO54；GPIO号属于P4，不是C5同号引脚，按实板版本复核。 支持2.4 GHz / 5 GHz 双频 Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。 [本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DB/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-DB/Product-Overview.md`。
- **相关报错/日志**：⚠️ 1080p@30fps 是模块能力上限之一，不应直接承诺端到端效果；实际码率、距离、路由器、手机解码和连续运行需实测。M-KIT-B 是否随套装附带 OV5647应以当期购买清单为准。
