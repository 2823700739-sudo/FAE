---
产品: ESP32-P4-Core-32MB
tags: [FAE, ESP32-P4, SDIO, 底板, 封装]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Core-32MB.htm
---

# ESP32-P4-Core-32MB

> [!important] ⚠️ 产品参数 / 规格优先
> 对外答复前核对准确型号、硬件/固件版本及⚠️/🔍标记；资料与源码核对不等于实板测试。

## ⚠️ 产品参数 / 规格（优先查看）

### SKU 36097 能否照搬整板 GPIO14至19 接 C6，有无底面焊盘
- **客户问题/现象**：自制底板希望沿用P4-WIFI6-POE-ETH的C6接法，索要GPIO14～19焊盘号、版本对应和正式封装。
- **涉及产品/型号**：ESP32-P4-Core-32MB，SKU36097；不是ESP32-P4-Module，也不是Core-DEV-KIT。
- **根因**：零售核心板的对外焊盘与裸P4芯片管脚、整板原理图符号不是同一编号体系。公开76脚定义从15脚GPIO13跳到16脚GPIO20，未列GPIO14～19。
- **回复内容（解决方法）**：本次再核对[Core官方引脚表](https://docs.waveshare.net/ESP32-P4-Core/)仍未列GPIO14～19，不提供虚构焊盘号，也不能把底面过孔当回流焊盘。公开原理图标为16MB版，不能据此断言POE整板和零售32MB模组同PCB/BOM；需厂家按批次确认。原问答引脚图按屏蔽罩面顶视，1脚ESP_EN左上，底视须镜像；尺寸图/STEP不能替代含焊盘、阻焊和钢网要求的生产land pattern。外接C6可评估P4 Slot1的GPIO Matrix：候选CLK12(焊盘14)、CMD13(15)、D0=8(10)、D1=9(11)、D2=10(12)、D3=11(13)，分别接C6 GPIO19/18/20/21/22/23；P4 GPIO54(72)候选接C6 EN。先确认这些脚未占用、供电/启动状态与复位电气，Hosted选C6/SDIO/Slot1/4-bit并配匹配从机，CMD及D0～3按电气要求上拉3.3V、低时钟起测。[SDMMC文档](https://docs.espressif.com/projects/esp-idf/en/stable/esp32p4/api-reference/peripherals/sdmmc_host.html)。此表是设计候选，非微雪已验证底板参考。
- **相关报错/日志**：🔍 来源：2026-11-16《确认 P4-Core-32MB 的 C6 接口》；无正式封装库、板次等同确认或外接C6实测。本次仅复核公开引脚表和Slot1映射能力。

## 其他问题
