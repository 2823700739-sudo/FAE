---
产品: ESP32-S3-Touch-AMOLED-1.43C
tags: [FAE, ESP32-S3, AMOLED, BLE, 配对]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-AMOLED-1.43C.htm
---

# ESP32-S3-Touch-AMOLED-1.43C

### 开发板与手机 App 的配对协议能否自定义
- **客户问题/现象**：希望自定义开发板与自研 App 的发现、连接、绑定和数据协议。
- **涉及产品/型号**：ESP32-S3-Touch-AMOLED-1.43C。
- **根因**：ESP32-S3 支持 BLE，不支持经典蓝牙；BLE 标准安全配对层与应用层绑定协议需要区分。
- **回复内容（解决方法）**：可以自定义 App 应用层协议，包括 Service/Characteristic UUID、数据帧、设备认证、密钥交换、绑定/解绑和权限。标准 BLE SMP 本身不能任意修改，但可配置绑定、PIN/Passkey、Numeric Comparison、LE Secure Connections 和加密等级。建议保留 BLE 标准加密配对，再叠加随机数挑战、设备唯一密钥、会话密钥和重放保护；开发板固件与 App 必须实现同一协议。
- **相关报错/日志**：无。
