---
产品: ESP32-P4-WIFI6-POE-ETH
tags: [FAE, ESP32-P4, ESP32-C6, BLE, ESP-Hosted]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-POE-ETH.htm
---

# ESP32-P4-WIFI6-POE-ETH

### C6 Hosted 声明支持 BLE HCI，但蓝牙控制器初始化无确认
- **客户问题/现象**：Wi-Fi可用且C6上报HCI能力，但P4启动蓝牙控制器失败，希望确认出厂Hosted版本。
- **涉及产品/型号**：ESP32-P4-WIFI6-POE-ETH、ESP32-C6。
- **根因**：微雪未公开批次版本表；“支持HCI”不等于主从初始化已经成功，仍需核对两端版本和日志。
- **回复内容（解决方法）**：通过C6 UART抓完整启动日志，查 `ESP-Hosted-MCU Slave FW version :: X.Y.Z`；P4端也可在Hosted就绪后调用版本查询API。用户后续实测日志确认该块C6为 `0.0.6`；若从未重刷或OTA，则可视为该实物保留的出厂版本。下一步对照P4端 `dependencies.lock` 的 `esp_hosted`版本及蓝牙失败日志，不应仅凭版本号判定硬件或固件不兼容。
- **相关报错/日志**：实测：`I (...) fg_mcu_slave: ESP-Hosted-MCU Slave FW version :: 0.0.6`。

