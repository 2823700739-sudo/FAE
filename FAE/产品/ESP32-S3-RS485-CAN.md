---
产品: ESP32-S3-RS485-CAN
tags: [FAE, ESP32-S3, PSRAM]
---

# ESP32-S3-RS485-CAN

> [!important] ⚠️ 产品参数 / 规格优先
> 参数与兼容性按准确型号和硬件/固件版本确认；保留未实测及待核实标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 默认 PSRAM 容量与测试固件是否启用
- **客户问题/现象**：要求核对提供的DemoTest压缩包PSRAM。
- **涉及产品/型号**：ESP32-S3-RS485-CAN、所附DemoTest快照。
- **根因**：型号ESP32-S3R8对应8MB Octal PSRAM，固件是否启用需另外看构建。
- **回复内容（解决方法）**：历史检查所附BIN含PSRAM=enabled/Octal驱动，源码CAN/RS485缓冲使用MALLOC_CAP_SPIRAM；该文件快照按8MB八线配置，不能仅凭字符串保证实板容量/稳定性。
- **相关报错/日志**：🔍 2026-10-28文件检查，未上板测内存。未保存内网路径。[产品资料](https://docs.waveshare.net/ESP32-S3-RS485-CAN/)。
