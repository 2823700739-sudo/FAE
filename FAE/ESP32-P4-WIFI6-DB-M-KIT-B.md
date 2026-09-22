---
产品: ESP32-P4-WIFI6-DB-M-KIT-B
aliases: [ESP32-P4-WIFI6-DB]
tags: [FAE, ESP32-P4, ESP32-C5, 摄像头, Wi-Fi, OTA]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-DB-M-KIT-B.htm
---

# ESP32-P4-WIFI6-DB-M-KIT-B

### 能否通过双频 Wi-Fi 把摄像头视频传到手机并支持 OTA
- **客户问题/现象**：希望由 P4 采集摄像头数据，经 2.4/5GHz Wi-Fi 上传到手机应用，并为后续 P4 与 C5 固件提供 OTA。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB-M-KIT-B、ESP32-P4、ESP32-C5、OV5647。
- **根因**：P4 提供 2-lane MIPI-CSI、ISP 和 H.264/JPEG 硬件能力，板载 C5 通过 SDIO/ESP-Hosted 为 P4 提供 2.4GHz/5GHz Wi-Fi 6。该硬件组合具备实现条件，但官方摄像头和网络示例是分开的，不是烧录即用的完整手机视频方案；P4 与 C5 也是两个独立固件目标。
- **回复内容（解决方法）**：建议先购买一套做 PoC：使用 OV5647、720p 15～30fps、H.264 单路上行，手机与开发板先连接同一路由器并用 RTSP 验证，再扩展 WebSocket/WebRTC、公网鉴权等。也可使用 SoftAP 让手机直连；C5 不支持 Wi-Fi Direct/P2P。“双频”表示可选择 2.4GHz 或 5GHz，并非同时双频工作。P4 使用 HTTPS OTA、双 OTA 分区、启动确认和自动回滚；C5 的 ESP-Hosted 固件需单独 Slave OTA，并保留 C5 UART 作为救砖通道。嵌入式端需要整合采集、编码、缓存和网络发送，手机软件负责连接、解码和显示。
- **相关报错/日志**：⚠️ 1080p@30fps 是模块能力上限之一，不应直接承诺端到端效果；实际码率、距离、路由器、手机解码和连续运行需实测。M-KIT-B 是否随套装附带 OV5647应以当期购买清单为准。
