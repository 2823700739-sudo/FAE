---
产品: ESP32-P4-POE-ETH
aliases: [ESP32-P4-ETH系列]
tags: [FAE, ESP32-P4, PoE, Ethernet]
商品链接: https://www.waveshare.net/shop/ESP32-P4-POE-ETH.htm
---

# ESP32-P4-POE-ETH

### PoE 电压是多少，是否只走网线 1/2/3/6
- **客户问题/现象**：询问PoE输入/输出电压和Mode A/Mode B线对。
- **涉及产品/型号**：ESP32-P4-POE-ETH、ESP32-P4-ETH。
- **根因**：原理图确认PoE模块向主板输出 `VCC1_5V`，并把 RJ12、RJ36、RJ45、RJ78 都送入模块；具体模块输入范围未在该产品公开文档单独标明。
- **回复内容（解决方法）**：使用标准 IEEE 802.3af PoE交换机/注入器（常见标称48V），不要用未经确认的12V/24V被动PoE。百兆数据走1/2、3/6；Mode A可在数据线对幻象供电，Mode B用4/5、7/8空闲线对。板卡同时接入两组，实际模式由PSE决定，不由软件选择。
- **相关报错/日志**：🔍 历史所述37～57V、5V/2.5A来自同系列PoE模块资料；只有确认模块料号相同后才能直接套用。5V输出可由本板原理图确认。

