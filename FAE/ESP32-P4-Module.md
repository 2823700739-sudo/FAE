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

### SOM 模组是什么，自制底板如何连接和烧录
- **客户问题/现象**：客户准备为 ESP32-P4-Module 设计底板，询问 SOM 含义、最小系统接线，以及是否只能通过 USB 下载、能否保留 UART0 下载。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-P4、ESP32-C6。
- **根因**：该产品属于 SOM（System on Module，系统级模组）：已集成 P4、C6、Flash、PSRAM、晶振、核心电源和内部 SDIO，但仍需底板提供 3.3V、地、接口、BOOT/RESET及应用外围。ESP32-P4 同时支持原生 USB Serial/JTAG 与 UART0 下载，并非只能使用 USB。
- **回复内容（解决方法）**：最小底板应将模组 85/86 的 `ESP_3V3` 都接稳定 3.3V，84 `VBAT` 无独立后备电源时接 3.3V，1/3/15/33/40/47/52/83 全部接地；87 `ESP_EN` 预留复位按键，63 `GPIO36/BOOT_EN` 外接约 10kΩ 上拉，62 `GPIO35/BOOT` 建议上拉并预留按键到 GND。原生 USB 下载使用模组 50 `GPIO24/D-`、51 `GPIO25/D+`；UART0 下载使用 64 `GPIO37/U0TXD`、65 `GPIO38/U0RXD`，连接 USB转串口或测试点，并保留 BOOT/RESET。USB VBUS 5V 必须先稳压到 3.3V，不能直接接 ESP_3V3。若需维护 C6，还应预留模组 4/5 的 C6 UART、9 `C6_IO9`、10 `C6_IO8`；GPIO54保留给C6复位。模组66 `ESP_LDO_VO4` 是内部 LDO 输出，不是普通3.3V输入。
- **相关报错/日志**：🔍 引脚号是 88Pin 模组焊盘号，不是 P4 裸芯片封装脚；量产前应按实际模组硬件版本复核原理图、封装和射频设计。

### 板载 C6 通过哪些引脚向 P4 传输 Wi-Fi 与蓝牙
- **客户问题/现象**：阅读 ESP32-P4-Module V1.1 原理图时，询问 C6 哪些脚接 P4、蓝牙是否走串口或 GPIO 映射，以及 ESP-Hosted 的作用。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-P4、ESP32-C6、ESP-Hosted。
- **根因**：C6 负责无线射频，P4 与 C6 之间以 SDIO 为主通信链路；ESP-Hosted 是在该链路上传送 Wi-Fi 控制/数据和蓝牙 HCI 数据的软件方案，不是独立硬件总线。C6 的 UART0 引到模组焊盘供烧录/日志，不是本模组的蓝牙主通道。
- **回复内容（解决方法）**：按 [ESP32-P4-Module V1.1 原理图](https://files.waveshare.com/wiki/ESP32-P4-Module/ESP32-P4-Module-datasheet.pdf)核对：C6 `GPIO19/SDIO_CLK→P4 GPIO18`、`GPIO18/SDIO_CMD→GPIO19`、`GPIO20～23/DATA0～3→GPIO14～17`，另有 `C6 GPIO2→P4 GPIO6` 和 `C6 CHIP_PU/EN←P4 GPIO54`。蓝牙可理解为“P4 NimBLE Host → ESP-Hosted/SDIO → C6 蓝牙 Controller → 天线”，不需找所谓“蓝牙 GPIO”或将 C6 UART 接 P4 UART；启用 BLE 还需两端版本匹配且 C6 固件支持 Hosted HCI。`C6_IO4～9`、`C6_IO13～15`、`C6_U0RXD/TXD` 只是另行引到焊盘，不应按数字误认作接到 P4 同号 GPIO。
- **相关报错/日志**：🔍 映射依据 V1.1 图纸，其他硬件版次应复核。相关软件配置见 [[ESP32-P4-Module-DEV-KIT#如何通过板载 C6 实现 BLE，需要哪些组件]]。
