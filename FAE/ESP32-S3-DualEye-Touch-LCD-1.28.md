---
产品: ESP32-S3-DualEye-Touch-LCD-1.28
tags: [FAE, ESP32-S3, RTC]
---

# ESP32-S3-DualEye-Touch-LCD-1.28

> [!important] ⚠️ 产品参数 / 规格优先
> 参数与兼容性按准确型号和硬件/固件版本确认；保留未实测及待核实标记。

## ⚠️ 产品参数 / 规格（优先查看）

### Y1 是 RTC 吗，完全断电后能否保持时间
- **客户问题/现象**：截图询问晶振/RTC用途。
- **涉及产品/型号**：ESP32-S3-DualEye-Touch-LCD-1.28，勿与无Touch型号混用。
- **根因**：Y1为40MHz系统基准晶振，不是独立RTC；该图未配置独立RTC芯片。
- **回复内容（解决方法）**：日期时间可程序计时并联网同步；完全断电仍走时需外接带备用电池的RTC，并核对可用接口/电平。
- **相关报错/日志**：🔍 2026-10-28图纸结论，未实板测试。[产品图纸](https://files.waveshare.com/wiki/ESP32-S3-DualEye-Touch-LCD-1.28/ESP32-S3-DualEye-Touch-LCD-1.28.pdf)。
