---
产品: ESP32-DEV-KIT-XX 系列
tags: [FAE, ESP32, 固件, ESP-AT]
---

# ESP32-DEV-KIT-XX 系列

### DemoTest 示例压缩包是否包含 AT 固件
- **客户问题/现象**：检查ESP32-DEV-KIT-XX-DemoTest.zip中是否有AT固件。
- **涉及产品/型号**：ESP32-DEV-KIT-XX-DemoTest.zip。
- **根因**：当时检查的包只有Arduino/ESP-IDF基础例程，没有现成BIN、ESP-AT工程或AT命令处理代码。
- **回复内容（解决方法）**：该特定压缩包不能当作AT固件包；UART示例是串口收发测试，不能因出现UART就判定支持AT。需要AT时按实际模组Flash/PSRAM规格选乐鑫ESP-AT对应构建。
- **相关报错/日志**：🔍 结论仅针对当时所附压缩包，不推广到未来版本。

### TestDemo.bin 文件名能否证明是 AT 固件
- **客户问题/现象**：询问WROVER-IE的TestDemo.bin是否为AT固件。
- **涉及产品/型号**：ESP32-DEV-KIT-WROVER-IE-TestDemo.bin。
- **根因**：当时无法访问提供的网络共享，文件名本身不足以识别固件功能。
- **回复内容（解决方法）**：取得可读取的BIN后检查ESP-AT版本/命令特征，并按配套说明和实际串口响应验证；当前只保存“未确认”状态，不能把前一ZIP检查结果套到另一BIN。
- **相关报错/日志**：🔍 未读取该BIN，尚无AT/非AT结论；未保存内部共享地址。
