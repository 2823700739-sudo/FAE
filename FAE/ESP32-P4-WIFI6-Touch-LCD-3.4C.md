---
产品: ESP32-P4-WIFI6-Touch-LCD-3.4C
tags: [FAE, ESP32-P4, LCD, ADC, NTC]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-3.4C.htm
---

# ESP32-P4-WIFI6-Touch-LCD-3.4C

### 背面 40Pin 能接多少路 NTC，能否分别显示温度
- **客户问题/现象**：询问背面 40Pin 最多支持多少个 NTC 热敏电阻，以及各路温度能否分别显示。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-3.4C，硬件 rev1.1。
- **根因**：40Pin 表示接口触点数量，不是 40 路模拟输入。按当前公开原理图，接口引出 7 个 ADC 输入：GPIO20/21/22 对应 ADC1_CH4/5/6，GPIO49/50/51/52 对应 ADC2_CH0/1/2/3。
- **回复内容（解决方法）**：最多可直接接 7 路独立 NTC，并在程序中轮询 7 个 ADC 通道、分别换算后同时显示温度 1～7；这属于依次采样，不是严格同步采样。每个 NTC 都要配独立分压电阻，使用 3.3V 分压，不能向 GPIO 输入 5V。建议做 ADC 校准和平均滤波，并按 NTC 的 B 值或 Steinhart–Hart 参数换算。超过 7 路时可通过 I2C 多通道 ADC 或模拟多路复用器扩展。
- **相关报错/日志**：🔍 7 路结论基于当前公开 rev1.1 原理图；量产前应再次核对实际板卡版本。
