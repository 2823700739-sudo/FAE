---
产品: ESP32-P4-Module
tags: [FAE, ESP32-P4, Module, UART]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Module.htm
---

# ESP32-P4-Module

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 如何控制 C6 GPIO，ESP-Hosted 0.0.6 有远程 GPIO 命令吗
- **客户问题/现象**：询问GPIO调用及输入输出命令格式，随后提供esp_hosted-0.0.6(1).zip要求核对C6端接口。
- **涉及产品/型号**：ESP32-P4-Module、ESP-Hosted 2.12.12/所附0.0.6源码快照、GPIO Expander。
- **根因**：P4本地GPIO与远端C6 GPIO属于不同控制器。新版Hosted有GPIO Expander RPC API，但所附0.0.6压缩包处理表/协议未实现通用GPIO配置读写；源码出现gpio_config不等于支持远程命令。
- **回复内容（解决方法）**：P4本地使用ESP-IDF gpio_set_direction/set_level/get_level。远端功能需核对主从版本及GPIO Expander配置：2.12.12所核对API头为esp_hosted_cp_gpio.h，使用esp_hosted_cp_gpio_reset_pin、set_direction（H_CP_GPIO_MODE_INPUT/OUTPUT）、set_level及get_level(pin,&level)；pin是C6 GPIO号，不是模组焊盘号，读取值从指针返回，函数返回错误码。这是C API，不是可发送的串口文本。该能力自2.10.0引入，不适用于所附旧快照；若保留0.0.6，要同时补协议字段、C6请求处理/返回及P4封装，不能只改一端。C6自己控制GPIO则直接用本地GPIO API，不必RPC。避开SDIO/复位/启动等占用脚。[官方更新记录](https://github.com/espressif/esp-hosted-mcu/blob/main/docs/changelog.md)。
- **相关报错/日志**：🔍 压缩包静态检查未编译/实测；出厂二进制不能仅凭“0.0.6”名字判为精确对应源码，也未确认出厂已支持GPIO Expander。来源：2026-10-20“确认 ESP32-P4 GPIO 调用方式”及提供源码后的纠正。

### 模块有多少路硬件 UART
- **客户问题/现象**：商品页面只写支持 UART，没有说明具体数量，客户询问模块有几路串口。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-P4。
- **根因**：Waveshare 商品页没有标数量；数量来自 ESP32-P4 数据手册。芯片包含 5 路 HP UART（UART0～UART4）和 1 路 LP UART。
- **回复内容（解决方法）**：主控共提供 6 路硬件 UART：UART0～UART4 五路高性能 UART，加一路低功耗 LP UART。UART0～UART4 可通过 GPIO Matrix 映射；“6 路控制器”不代表模块上有 6 个独立串口插座，实际可同时使用数量需结合模块引脚是否引出、GPIO 复用和板级外设占用判断。板载或外接 C6 的串口不计入这 6 路。
- **相关报错/日志**：无。

### 板载 C6 通过哪些引脚向 P4 传输 Wi-Fi 与蓝牙
- **客户问题/现象**：询问 C6 与 P4 的通信、蓝牙传输及 ESP-Hosted 作用，并追问六根 SDIO 是否已引出。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-P4、ESP32-C6、ESP-Hosted。
- **根因**：C6 负责无线射频，P4 与 C6 之间以 SDIO 为主通信链路；ESP-Hosted 是在该链路上传送 Wi-Fi 控制/数据和蓝牙 HCI 数据的软件方案，不是独立硬件总线。C6 的 UART0 引到模组焊盘供烧录/日志，不是本模组的蓝牙主通道。
- **回复内容（解决方法）**：按 [ESP32-P4-Module V1.1 原理图](https://files.waveshare.com/wiki/ESP32-P4-Module/ESP32-P4-Module-datasheet.pdf)核对：C6 `GPIO19/SDIO_CLK→P4 GPIO18`、`GPIO18/SDIO_CMD→GPIO19`、`GPIO20～23/DATA0～3→GPIO14～17`，另有 `C6 GPIO2→P4 GPIO6` 和 `C6 CHIP_PU/EN←P4 GPIO54`。蓝牙可理解为“P4 NimBLE Host → ESP-Hosted/SDIO → C6 蓝牙 Controller → 天线”，不需找所谓“蓝牙 GPIO”或将 C6 UART 接 P4 UART；启用 BLE 还需两端版本匹配且 C6 固件支持 Hosted HCI。`C6_IO4～9`、`C6_IO13～15`、`C6_U0RXD/TXD` 只是另行引到焊盘，不应按数字误认作接到 P4 同号 GPIO。
  引出补充：P4 GPIO14～19 六根 SDIO 已在模组内部连接，未引到外部 88Pin，底板无需重连且不能从模组焊盘另用它们。GPIO6/54虽引出，仍连接C6控制/复位，使用无线时应保留。
  低功耗补充：C6 GPIO2→P4 GPIO6可用于C6唤醒休眠中的P4；GPIO54→C6 EN才是复位。需主从固件启用并正确配置唤醒功能，不是连线后自动生效，常规数据仍走SDIO。所给板图为R52=0Ω唤醒线、R54=0Ω复位线，位号不能跨板照搬。[Hosted 2.8.3低功耗示例](https://components.espressif.com/components/espressif/esp_hosted/versions/2.8.3/examples/host_network_split__power_save?language=en)。来源：2026-10-21“查明 GPIO6 与 C6 GPIO2 的用途”。
  增量补充（2026-10-31整理）：2026-10-28 Module-U软件入口：先用Platform examples/esp-idf/10_wifistation，目标esp32p4，配置2.4GHz凭据并保留Hosted/esp_wifi_remote和板级SDIO；P4应用使用esp_wifi接口。已有匹配C6固件时日常只更新P4，擦除/刷其他C6固件后需恢复；自制底板另核对供电/复位/天线，未客户底板联调。
- **相关报错/日志**：🔍 映射依据 V1.1 图纸，其他硬件版次应复核。相关软件配置见 [[ESP32-P4-Module-DEV-KIT#如何通过板载 C6 实现 BLE，需要哪些组件]]。

### 使用百兆网口会与板载 C6 Wi-Fi 的 SDIO 引脚冲突吗
- **客户问题/现象**：自制底板担心使用百兆网后无法使用Wi-Fi。
- **涉及产品/型号**：ESP32-P4-Module/Module-U、RMII、ESP-Hosted。
- **根因**：内部C6用P4 GPIO14～19的SDIO，不是把所有SDIO/RMII复用候选脚都占用。官方底板的RMII可避开该组信号。
- **回复内容（解决方法）**：可参考官方底板：TX_EN49、TXD0/1=34/35、CRS_DV28、RXD0/1=29/30、REF_CLK50，MDC31、MDIO52、PHY_RST51；保留GPIO6/54给C6控制。RMII数据信号需选芯片允许的候选脚，不能任意映射；软件分别初始化ETH及Hosted Wi-Fi。自制底板的设计审核、程序联调和整机验证由客户负责，可提供参考图纸。[底板图](https://files.waveshare.com/wiki/ESP32-P4-Module-DEV-KIT/ESP32-P4-Module-DEV-KIT.pdf)。
- **相关报错/日志**：⚠️ 图纸支持同时使用，不代表客户底板已经双网络实测；数字为P4 GPIO，不是模组焊盘。

### 同名 V1.1 图纸是否已完成 Rev3.2 硬件适配
- **客户问题/现象**：对比公开模组图与客户提供的新V1.1图，询问是否适用于P4 Rev3.2。
- **涉及产品/型号**：ESP32-P4-Module V1.1、ESP32-P4NRW32/NRW32X、R1、MP1605。
- **根因**：旧图U8-54的核心电源跳线R1标NC；新图改0Ω、芯片标NRW32X，C111改1pF，但两图DCDC仍标R32/R35=200kΩ、C27=NC。图纸名称/芯片后缀不足以确认实板revision。
- **回复内容（解决方法）**：新图已接通54脚到ESP_VDD_HP，不能接3.3V。Rev3.x供电设计还需结合MP1605确认反馈/补偿网络和稳定性，不要把乐鑫参考电路的499kΩ/22pF不加分析地照搬到另一DCDC。核对实装BOM、芯片识别和硬件验证记录；固件按Rev3.x成套重编。[乐鑫硬件指南](https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32p4/schematic-checklist-esp32p4.html)。
- **相关报错/日志**：🔍 新旧图均名V1.1，不能据此宣布整体已通过Rev3.2验证。C111为C6射频匹配变更，不能证明P4供电已验证。

### SOM 模组是什么，自制底板如何连接和烧录
- **客户问题/现象**：客户准备为 ESP32-P4-Module 设计底板，询问 SOM 含义、最小系统接线，以及是否只能通过 USB 下载、能否保留 UART0 下载。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-P4、ESP32-C6。
- **根因**：该产品属于 SOM（System on Module，系统级模组）：已集成 P4、C6、Flash、PSRAM、晶振、核心电源和内部 SDIO，但仍需底板提供 3.3V、地、接口、BOOT/RESET及应用外围。ESP32-P4 同时支持原生 USB Serial/JTAG 与 UART0 下载，并非只能使用 USB。
- **回复内容（解决方法）**：最小底板应将模组 85/86 的 `ESP_3V3` 都接稳定 3.3V，84 `VBAT` 无独立后备电源时接 3.3V，1/3/15/33/40/47/52/83 全部接地；87 `ESP_EN` 预留复位按键，63 `GPIO36/BOOT_EN` 外接约 10kΩ 上拉，62 `GPIO35/BOOT` 建议上拉并预留按键到 GND。原生 USB 下载使用模组 50 `GPIO24/D-`、51 `GPIO25/D+`；UART0 下载使用 64 `GPIO37/U0TXD`、65 `GPIO38/U0RXD`，连接 USB转串口或测试点，并保留 BOOT/RESET。USB VBUS 5V 必须先稳压到 3.3V，不能直接接 ESP_3V3。若需维护 C6，还应预留模组 4/5 的 C6 UART、9 `C6_IO9`、10 `C6_IO8`；GPIO54保留给C6复位。模组66 `ESP_LDO_VO4` 是内部 LDO 输出，不是普通3.3V输入。
- **相关报错/日志**：🔍 引脚号是 88Pin 模组焊盘号，不是 P4 裸芯片封装脚；量产前应按实际模组硬件版本复核原理图、封装和射频设计。

## 其他问题

### 设置 20dBm 但 IPEX 实测 10dBm 如何定位
- **客户问题/现象**：客户同模组自制底板，功率设20dBm，IPEX读10dBm，追问天线报告能否证明功率。
- **涉及产品/型号**：ESP32-P4-Module-U、ESP32-C6、CA-C03匹配报告。
- **根因**：软件最大功率上限、实际速率功率及测量口径不同；差10dB需核查，不能直接认定正常或模组故障。
- **回复内容（解决方法）**：esp_wifi_set_max_tx_power单位0.25dBm，20dBm对应80，核对调用/返回/读取；不同速率实际功率有别。确认是模组自身还是底板IPEX，只有串入的馈线/匹配才纳入该测量路径。按固定信道/速率RF测试、包内平均功率、带宽总功率及线缆衰减补偿对比正常样品，并测发射时供电。20dBm包内、10%占空比时整段均值10dBm仅计算例子。天线报告VSWR/dBi增益/效率不等于dBm传导功率，不能用约30%效率解释该差异；报告没给20dBm设置下实测功率。
- **相关报错/日志**：🔍 2026-10-29问答，未给仪器/代码/波形，不判责任。[功率API](https://docs.espressif.com/projects/esp-idf/en/v5.5/esp32c6/api-reference/network/esp_wifi.html)。

### 怎样通过模组焊盘给 C6 烧录或恢复固件
- **客户问题/现象**：询问核心模组的 C6 可用哪些烧录方式。
- **涉及产品/型号**：ESP32-P4-Module、ESP32-C6、88Pin 焊盘。
- **根因**：C6 UART0 已引出；Slave OTA 则依赖 C6 正常运行及配套 Hosted/分区，不能救砖。
- **回复内容（解决方法）**：外接 3.3V TTL：模组4=C6 RX接适配器TX、5=C6 TX接RX，共地；9=C6 IO9在断电时接GND，10=C6 IO8保持上拉。P4也应停在下载模式，避免应用干扰C6 EN；自制底板按62/63的BOOT条件处理。先用115200、目标esp32c6，按固件包地址烧录；成功后断电拆IO9短接。保持P4/C6 Hosted成套，能正常通信时可评估SDIO Slave OTA。[模组资料](https://docs.waveshare.net/ESP32-P4-Module/)。
- **相关报错/日志**：🔍 焊盘号不是裸芯片脚号，未实板烧录。C6识别后切到460800失联时，先以115200读取flash-id，再用--no-stub对照，不能由C6 revision推断P4版本。

### 客户索要“32M 模组固件”时应先确认什么
- **客户问题/现象**：客户称使用图片中的32M内存模组，希望发送对应固件。
- **涉及产品/型号**：ESP32-P4模组，具体板次和固件用途未明确。
- **根因**：“32M”可能指PSRAM而非Flash；相同内存容量不代表适用同一底板/屏幕固件。
- **回复内容（解决方法）**：先确认完整型号、底板/屏幕、P4 revision、Flash与PSRAM容量，以及要出厂测试还是某功能例程。可回复“请确认需要出厂测试固件还是功能例程，32M是否指PSRAM”；信息齐全后按对应版本选包，不按口述容量直接发通用BIN。
- **相关报错/日志**：🔍 原问答只完成需求澄清，未确认应提供的固件或镜像。
