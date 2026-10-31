---
产品: ESP32-DEV-KIT-WROOM-32E-N4
tags: [FAE, ESP32, Mixly, 烧录, 自动下载]
商品链接: https://www.waveshare.net/shop/ESP32-DEV-KIT-WROOM-32E-N4.htm
---

# ESP32-DEV-KIT-WROOM-32E-N4

> [!important] ⚠️ 产品参数 / 规格优先
> 硬件接口能力不等于软件组合已验证；回复前核对准确板型、PCB及工具版本，保留⚠️/🔍标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 是否支持 Mixly 免按 BOOT 上传，如何识别自动下载电路
- **客户问题/现象**：询问Mixly能否免按BOOT上传，并要求说明怎样看自动下载原理图。
- **涉及产品/型号**：ESP32-DEV-KIT-WROOM-32E-N4、ESP32-DEV-KIT-XX系列、CH343P、Mixly。
- **根因**：板载CH343P及自动下载电路具备自动进入下载模式的硬件基础，但Mixly的具体板型/上传配置仍要正确控制DTR/RTS。仅有TX/RX不代表能自动下载。
- **回复内容（解决方法）**：在官方原理图搜索AUTO PROGRAM CIRCUIT：CH343P U4的12脚DTR、13脚RTS经T1/T2及电阻控制EN和GPIO0。复位时GPIO0保持低电平、EN释放后进入下载模式，下载软件可代替按BOOT。Mixly选择匹配普通ESP32板型、实际COM口及支持DTR/RTS自动复位的上传配置；若失败再手动下载作对照，核对日志和具体Mixly版本，不承诺所有版本即用。系列其他固件说明见[[ESP32-DEV-KIT-XX]]。[官方图纸](https://files.waveshare.com/wiki/ESP32-DEV-KIT-XX/ESP32-DEV-KIT-XX.pdf)、[自动下载原理](https://docs.espressif.com/projects/esptool/en/latest/esp32/advanced-topics/boot-mode-selection.html#automatic-bootloader)。
- **相关报错/日志**：⚠️ 确认硬件能力，尚无该板与具体Mixly版本配合实测。来源：2026-10-20“确认 Mixly 免按 BOOT 上传支持”。
