---
产品: ESP32-P4-Module
tags: [FAE, ESP32-P4, Module, UART]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Module.htm
---

# ESP32-P4-Module

### 模块有多少路硬件 UART
- **客户问题/现象**：商品页面只写支持 UART，没有说明具体数量，客户询问模块有几路串口。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-P4。
- **根因**：Waveshare 商品页没有标数量；数量来自 ESP32-P4 数据手册。芯片包含 5 路 HP UART（UART0～UART4）和 1 路 LP UART。
- **回复内容（解决方法）**：主控共提供 6 路硬件 UART：UART0～UART4 五路高性能 UART，加一路低功耗 LP UART。UART0～UART4 可通过 GPIO Matrix 映射；“6 路控制器”不代表模块上有 6 个独立串口插座，实际可同时使用数量需结合模块引脚是否引出、GPIO 复用和板级外设占用判断。板载或外接 C6 的串口不计入这 6 路。
- **相关报错/日志**：无。
