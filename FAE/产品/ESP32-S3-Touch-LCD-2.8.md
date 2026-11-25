---
产品: ESP32-S3-Touch-LCD-2.8
tags: [FAE, ESP32-S3, WiFi, BLE]
---

# ESP32-S3-Touch-LCD-2.8

> [!important] ⚠️ 产品参数 / 规格优先
> 对外答复前核对准确型号、硬件/固件版本及⚠️/🔍标记；资料与源码核对不等于实板测试。

## ⚠️ 产品参数 / 规格（优先查看）

### 能否连接手机 Wi-Fi 和蓝牙，能否当蓝牙音箱
- **客户问题/现象**：客户确认手机Wi-Fi和蓝牙能否连接。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-2.8，2.4GHz Wi-Fi、BLE。
- **根因**：无线硬件支持与具体手机交互固件不同；S3支持BLE而非经典蓝牙SPP/A2DP。
- **回复内容（解决方法）**：可开发连接手机2.4GHz热点的STA，或板建AP供手机网页/App交互；BLE需相应Service/Characteristic与手机App协议。不能当成已预装普通蓝牙串口SPP或A2DP音箱，也不保证仅配对就能传业务数据。[板卡资料](https://docs.waveshare.net/ESP32-S3-Touch-LCD-2.8/)、[乐鑫蓝牙说明](https://docs.espressif.com/projects/esp-idf/en/v5.2.2/esp32s3/api-guides/bluetooth.html)。
- **相关报错/日志**：🔍 来源：2026-11-14《确认手机连接 Wi-Fi 和蓝牙》；未给手机、业务固件和连接测试。

## 其他问题
