---
产品: ESP32-S3-Touch-LCD-1.83
tags: [FAE, ESP32-S3, 设备ID, eFuse]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-LCD-1.83.htm
---

# ESP32-S3-Touch-LCD-1.83

### 是否有每块板唯一的设备 ID
- **客户问题/现象**：需要设备注册或区分每块板。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-1.83。
- **根因**：板上没有额外序列号芯片，但ESP32-S3 eFuse有工厂写入的48位Base MAC。
- **回复内容（解决方法）**：可用 `esp_efuse_mac_get_default()` 读取6字节Base MAC作为设备ID。用于安全认证时不能把MAC当密钥，应另生成并安全保存设备密钥或证书。
- **相关报错/日志**：无。

