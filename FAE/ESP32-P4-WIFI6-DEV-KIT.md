---
产品: ESP32-P4-WIFI6-DEV-KIT
tags: [FAE, ESP32-P4, USB, 音频, 烧录]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-DEV-KIT.htm
---

# ESP32-P4-WIFI6-DEV-KIT

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 连接 8.8-DSI-TOUCH-A 所称“4号排线”是什么
- **客户问题/现象**：原沟通答复该板可以接8.8-DSI-TOUCH-A，使用“4号排线”，希望整理兼容方案。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、8.8-DSI-TOUCH-A；屏幕/板卡版本及排线料号未提供。
- **根因**：🔍 缺原配件图及排线定义；“4号”是上下文编号，不是可独立采购、接线和复现的规格。
- **回复内容（解决方法）**：保留原沟通“可接/4号排线”为待核实答复，不升级为本次已验证兼容。需补官方适配/BSP配置、准确排线型号/图号、两端针数/间距/触点/针序和供电/触摸要求；按准确硬件断电连接，确认显示与触摸后再标已适配。不能凭同为DSI或编号选线，也不借其他尺寸的配置作保证。
- **相关报错/日志**：状态：待确认兼容性与可追溯排线规格；无当次源码配置/实板结果。 来源：用户提供的本周支持汇总（2026-10-31入库，原始个案日期未注明）。

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DEV-KIT/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-DEV-KIT/Product-Overview.md`。核对日期：2026-10-31。

### P4 是否只能用 MIPI-DSI 驱动 LCD
- **客户问题/现象**：认为P4只能用DSI，询问该板能否接其他接口屏。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、RGB、I80、SPI/QSPI、MIPI-DSI。
- **根因**：芯片支持多种LCD接口，但开发板专用屏插座是2-lane MIPI-DSI，不能把芯片能力直接当成板上已有所有连接器。
- **回复内容（解决方法）**：配套屏/例程主要走DSI；RGB并口、8080/I80、SPI/QSPI屏可评估从40Pin空闲GPIO接线并适配驱动。先获取目标屏完整规格、控制器、电平、针序和时序，计算GPIO及显存/带宽占用，避开板载外设；不能把RGB/I80屏直接插DSI座。[P4 LCD文档](https://docs.espressif.com/projects/esp-idf/en/stable/esp32p4/api-reference/peripherals/lcd/index.html)、[板卡资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DEV-KIT/)。
  2026-11-04补充：2026-11-04《确认 MIPI2 屏幕通道兼容性》明确本板接280×1424候选屏：最多2条DSI数据lane，固定仅4-lane屏不能直接驱动；能否改2-lane取决于屏IC和模组配置。分辨率本身不能保证兼容，具体时序/供电/针序仍待规格书与实测，详见[[ESP32-P4-通用问题#4-lane MIPI 屏能否改为 2-lane 使用]]。
- **相关报错/日志**：🔍 未给目标屏，无具体兼容性/帧率实测。来源：2026-10-21“确认P4驱动LCD的接口”。

### USB1_5V 与 R21 由哪个 GPIO 控制，四个 USB-A 如何供电
- **客户问题/现象**：询问R21另一端接哪个GPIO，以及仅USB1键盘灯亮是否说明其余口没有5V。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、R21、U5 MMDT3906DW、U6 DIO7003。
- **根因**：USB1_5V指H2原生USB Type-C的VBUS输入，不是1号Type-A供电。R21=10kΩ接GND与U5的2/5/6公共节点，属于Q5/U5输入电源电路，不接P4 GPIO。四Type-A共用VCC_5V→U6→VBUS_OUT，EN经R25上拉，无需GPIO单独开启。
- **回复内容（解决方法）**：供电正常且无过载时四USB-A应有约5V，跳线切换的是数据通路。用USB电压表/转接测试板测VBUS，避免表笔伸入插座短路；键盘LED通常受枚举/Host命令控制，灯灭不能证明没电。若仅1号有电、后三口确实无电，按实板版本查共用供电支路、插座和走线，不找所谓R21控制GPIO。两个Type-C中USB TO UART为CH343调试；原生Type-C有CC下拉和受电路径，无向键盘输出Host 5V设计，普通转接头不能直接当键盘口。[原理图](https://files.waveshare.net/wiki/ESP32-P4-WIFI6-DEV-KIT/ESP32-P4-WIFI6-DEV-KIT.pdf)。
- **相关报错/日志**：🔍 已核对公开图纸，客户实板VBUS未测。数据跳线/枚举检查见[[ESP32-P4-WIFI6-DEV-KIT#USB 设备直连 1 号口正常，经 CH334 HUB 的 2～4 号口无法识别]]。来源：2026-10-21“确认 USB1_5V GPIO 与 USB 供电”。

### 主控有几路 UART，板上有几个串口插座
- **客户问题/现象**：询问板子有几个串口。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、UART0～4、LP UART。
- **根因**：P4含5路高性能UART及1路LP UART，控制器数量不等于板上插座数。
- **回复内容（解决方法）**：一个Type-C UART用于烧录/日志；其余UART按资源和GPIO Matrix映射到空闲排针。C6 UART焊盘属于另一颗芯片，不计入P4六路。映射前核对板载外设占用，使用3.3V TTL。
- **相关报错/日志**：无；来源为客户串口数量问答。

### 16MB Flash 能否换焊为 32MB
- **客户问题/现象**：希望把图中的Flash升级为32MB。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、U9、W25Q128JVSIQ。
- **根因**：所查图纸U9为16MB、3.3V SPI NOR；32MB需256Mbit器件并匹配封装、脚位、时序及驱动。
- **回复内容（解决方法）**：可评估硬件换焊；先核对替换芯片完整型号和封装，Flash Size改32MB、调整分区并重编。验收容量识别、正常启动及超过16MB地址范围的擦写读回，不能只改软件容量。[原理图](https://files.waveshare.com/wiki/ESP32-P4-WIFI6-DEV-KIT/ESP32-P4-WIFI6-DEV-KIT-datasheet.pdf)。
- **相关报错/日志**：⚠️ 未验证换焊组合，板次与实装Flash先确认。

### 以太网 PHY 芯片型号
- **客户问题/现象**：询问板载网口 PHY 型号和速率。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：硬件规格确认。
- **回复内容（解决方法）**：板载 PHY 为 `IP101GRI`，通过 RMII 连接 ESP32-P4，支持 10/100 Mbps 以太网。
- **相关报错/日志**：无。

### 3.5 mm 耳机口是单声道吗，插座型号是什么
- **客户问题/现象**：询问耳机口声道数、是否为 PJ320，以及在哪里查。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：板载 ES8311 只有一路 DAC；原理图 J1 标注为 `PJ-393，8 脚贴片`。
- **回复内容（解决方法）**：耳机口为单声道，左右耳播放相同声音，不支持独立立体声。采购替换件应按 PJ-393 的 8 脚封装、引脚定义及插入检测脚选型，不能仅凭 3.5 mm 外形替换为 PJ320；可在官方原理图第 1 页 J1 位置核对。
- **相关报错/日志**：无。

### GPIO26、48、53、47 能否采集或输出模拟信号
- **客户问题/现象**：想将四个 GPIO 用作模拟输入，或重映射为 I2S 后输出模拟音频。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：只有 GPIO53 支持 ADC2_CH4，但它还连接板载功放控制；P4 无内置 DAC。I2S 引脚输出的是数字音频。
- **回复内容（解决方法）**：采集外部模拟电压时只有 GPIO53具备 ADC 能力，但应先处理功放控制占用；其他三脚不支持 ADC。若输出模拟音频，应使用“GPIO I2S → 外接 I2S DAC”，或直接使用板载 ES8311 与耳机口，不要把 I2S 数字信号当模拟信号。
- **相关报错/日志**：无。

### CSI_IO0 和 CSI_IO1 接到哪些引脚，能否软件控制
- **客户问题/现象**：询问摄像头接口的 CSI_IO0/CSI_IO1 含义及控制能力。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、J4、CSI_IO0、CSI_IO1。
- **根因**：辅助信号名不是 P4 GPIO0/1，也不是图像差分数据线。按 2026-10-08 核对的完整官方图纸，J4-11 CSI_IO0 经 R53（10kΩ）上拉到 3.3V；J4-12 CSI_IO1 的 R55 标 NC，板端悬空；均未接 P4 GPIO。
- **回复内容（解决方法）**：按原板接线，程序不能直接控制这两条辅助线。摄像头端用于 RESET、PWDN 或使能仍需查具体模组原理图，不能仅看网络名决定。需要硬件复位时应设计可控 GPIO/开漏或独立供电通路并核对电平，不能只在驱动中填写 GPIO 编号；IO1 测到高电平时，还应查摄像头侧是否内部上拉。[本型号完整原理图](https://files.waveshare.net/wiki/ESP32-P4-WIFI6-DEV-KIT/ESP32-P4-WIFI6-DEV-KIT.pdf)。
- **相关报错/日志**：🔍 引脚、阻值和 NC 装配以对应实板版次为准，具体摄像头用途未确认。来源：2026-10-08 CSI_IO 客户问答；通用说明见 [[ESP32-P4-通用问题#摄像头接口 CSI_IO0 与 CSI_IO1 是否用于复位]]。

### 四个 USB 口中哪个支持 OTG Device，另一个 Type-C 能否复用
- **客户问题/现象**：确认四个 Type-A 与 `USB1.1 Type-C` 的 Host/Device 能力。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：四个 Type-A 属于 USB 2.0 HS OTG 通路；`USB1.1 Type-C` 连接 GPIO24/25，默认用于 USB-Serial/JTAG，硬件 CC 下拉使其更适合作为设备端。
- **回复内容（解决方法）**：`USB1.1 Type-C` 可由 TinyUSB/USB Device Stack 复用为 CDC/HID/MSC 等 Device，但只有 Full-Speed 12 Mbps，启用后会占用原 USB-Serial/JTAG。Type-C UART 口只是 CH343 串口，不能复用为 OTG。四个 Type-A 的 HS 理论速率为 480 Mbps。
- **相关报错/日志**：无。

### 不接 Type-C 能否从 40Pin 给整板和摄像头供电
- **客户问题/现象**：DEV-KIT-B希望用杜邦接口给整板及CSI摄像头供电。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT/B、40Pin。
- **根因**：40Pin的5V接板上公共电源轨，可供整板降压，CSI摄像头再从板端供电。
- **回复内容（解决方法）**：外部稳定5V接物理2或4脚5V，GND接例如6脚；CSI摄像头通常不另接电源。建议按负载先选5V/3A并用短粗线，注意屏幕启动压降。5V不得接3V3/GPIO，需多入口时先设计隔离。[资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DEV-KIT/Resources-And-Documents/)。
- **相关报错/日志**：⚠️ 图纸接线结论未实板供电；3A是供电余量建议，不是整板耗电或排针额定值。

### RTC 电池口能否给整板供电，如何使用电池和 PoE
- **客户问题/现象**：询问锂电池规格、整机移动供电及网口PoE。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、RTC电池、PoE HAT (C)。
- **根因**：板载电池口用于RTC保持时间，不是整机锂电池供电入口；PoE需安装匹配的取电模块。
- **回复内容（解决方法）**：整机可用5V充电宝或合规外置电源模块经Type-C供电，不能把动力电池接RTC口。官方展示PoE HAT(C)，需核对板次/安装及总功耗并另购模块和标准PSE。此前提及UPS Module 3S仅作外置5V方案参考，组合未实测。[板卡资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DEV-KIT/)。
- **相关报错/日志**：🔍 可充电RTC电池按该接口规格选择；具体外置电池/PoE组合未验证。

### 两路 MIPI 摄像头或 MIPI 加 DVP 能否同时采集
- **客户问题/现象**：希望接两路MIPI，并追问是否有DVP接口。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、CSI、DVP、UVC。
- **根因**：原板只有一路2-lane CSI；另一DSI用于显示，不是第二摄像头。P4支持DVP，但本板没有专用DVP插座。
- **回复内容（解决方法）**：双摄可评估CSI+USB UVC，或从空闲GPIO转接DVP；8位DVP通常还需D0～7、PCLK/VSYNC/HREF/XCLK及I2C。自行核对IO、驱动、供电、采集/缓存资源，不可把2-lane视为两摄像头。[驱动资料](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32p4/api-reference/peripherals/camera_driver.html)。
- **相关报错/日志**：⚠️ 双路方案未实测，不保证分辨率、帧率或稳定性。

### 是否支持 Modbus RTU 或 TCP
- **客户问题/现象**：询问开发板能否用Modbus连接设备。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、ESP-Modbus。
- **根因**：Modbus由软件实现，本板有以太网/无线网络，但没有板载RS485收发器。
- **回复内容（解决方法）**：用ESP-Modbus实现RTU/TCP主从站。TCP可走板载百兆网或C6 Wi-Fi；RTU从UART外接3.3V逻辑兼容RS485收发器，再接A/B。核对波特率、校验、站号及寄存器表。[官方组件](https://github.com/espressif/esp-modbus)。
- **相关报错/日志**：🔍 支持协议开发不代表客户设备已联调。

### 能否接外部 USB UAC 麦克风
- **客户问题/现象**：希望使用外部 USB 麦克风，询问 P4 开发板能否支持 UAC 音频采集。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、USB Host、UAC 1.0。
- **根因**：该板有 USB-A OTG Host 通路；乐鑫 `usb_host_uac` 1.5.0 支持 ESP32-P4 和 UAC 1.0 音频设备，但固件须安装 Host 栈及 UAC 类驱动，板载麦录音程序不会自动切换到 USB 音频。
- **回复内容（解决方法）**：按PCB版次选择1号直连通路：Rev1.1跳线接DEVICE、Rev1.2接HOST（不能统一写接HOST），先用官方 `audio_player/usb_audio_player` 的麦克风接收示例验证；枚举后核对实际采样率、位深、声道数并启用接收流。仅支持 UAC 2.0 的设备不能按该驱动直接保证兼容。2～4 号经 CH334 HUB，应另核对固件 HUB 支持及设备速度条件。[官方驱动 1.5.0](https://components.espressif.com/components/espressif/usb_host_uac/versions/1.5.0/readme?language=en)、[麦克风接收示例](https://components.espressif.com/components/espressif/usb_host_uac/versions/1.5.0/examples/audio_player?language=en)。
- **相关报错/日志**：⚠️ 具体 USB 麦克风型号未知，本板与该设备组合未实测。来源：2026-10-08“确认ESP32-P4的UAC麦克风支持”；2026-10-23依据官方FAQ更正版本跳线，见本页HUB条目。

### J11、J1 音频路径和软件调音量
- **客户问题/现象**：询问喇叭接口、耳机接口、功率和音量调节关系。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：音频路径为 P4 I2S → ES8311；J1 为未经过功率放大的耳机输出，J11 经 NS4150B 功放驱动喇叭。
- **回复内容（解决方法）**：J11 接喇叭，8Ω 下实际输出不应简单按“3W”理解；J1 接耳机或外部功放输入。通过 I2C 配置 ES8311 音量会同时影响输出；GPIO53主要负责功放使能。插入耳机时按原理图会关闭喇叭功放。
- **相关报错/日志**：无。

### 用外置麦克风替代板载麦克风，应从哪里接线
- **客户问题/现象**：希望用外置麦克风或插入的耳机麦替代默认板载 MIC；询问 `MIC_P′`、R69/R70 的作用、如何隔离板载 MIC，以及为何不拆 R72。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：按客户截图及中文官网所链接的图纸，耳机麦路径为 `J1-6 → MIC_P′ → R70 → MIC_P → C73 → ES8311 MIC1P`；R69 可将 MIC_P 接模拟地。板载 MIC1 经 L8、C97 也接 MIC_P′，没有自动麦克风选择开关。撇号只是网络名区分，不代表反相。R72（0Ω）为 `A3V3 → ES8311 AVDD（11脚）` 的模拟供电连接，拆它会影响 Codec 录音和播放，而板载 MIC1 仍可由 A3V3 供电。此前“R69/R70 只属于输出网络、与麦克风无直连”的结论来自另一份图纸，不适用于本次截图所示连线。
- **回复内容（解决方法）**：使用耳机麦前先按实物版次与插头 CTIA/OMTP 接点核对图纸和 BOM。按本次图纸，可在断电后拆 L8 隔离板载 MIC；确认 R70 装 0Ω、R69 不装，并保留 R72。`0R/NC`、`NC/0R` 表示可选装配，不证明实物已经如此焊接；`EARPHONE_DETECT` 只是插入检测，不能据此自动切换录音源。普通耳机麦需按规格补充合适的偏置供电和电阻，2.2kΩ 仅为常见参考，不能直接将电源硬接 MIC_P′；本图未见专门耳机麦偏置电路。用微雪 `12_I2SCodec` 的非 BSP `echo` 模式验证录音回放，核对本板 I2C/I2S 接线。若仅将原板载麦移到板外，可拆 MIC1 后把同类型、兼容其供电的有源模拟麦接原 `VDD/GND/DAT` 焊盘；这与普通耳机麦偏置接法不同。40Pin 未直接引出模拟麦输入；也可接外置 I2S 数字麦并改程序。[中文官网图纸](https://files.waveshare.net/wiki/ESP32-P4-WIFI6-DEV-KIT/ESP32-P4-WIFI6-DEV-KIT.pdf)、[音频示例说明](https://github.com/waveshareteam/ESP32-P4-Platform/blob/main/examples/esp-idf/12_I2SCodec/README_CN.md)、[偏置电路参考](https://www.ti.com/document-viewer/lit/html/SSZT743)。
- **相关报错/日志**：⚠️ 耳机麦改线与偏置方案为图纸推导，尚未实机验证；客户未提供耳机型号和插头标准。🔍 同型号公开图纸存在连线差异，应以实板对应版本为准；R70 波形应先按本图直连关系排查，不能直接归因于软件回环。来源：2026-09-30 客户问题聊天“查找耳机麦克风启用说明”。

### 是否有可直接搭配的双目相机
- **客户问题/现象**：希望为开发板选择双目相机。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：板上只有一路 2-lane MIPI-CSI，官方例程以单目 OV5647 为主，没有官方验证的 MIPI 双目方案。
- **回复内容（解决方法）**：需要双目时可评估 OS02G10 Stereo USB Camera (A) 或 AR0144 Stereo USB Camera (A)，基于 ESP-IDF USB UVC Host 自行适配，并先以低分辨率 MJPEG（如 1280×480 或 640×240）验证。IMX219-83 Stereo Camera 为双路 MIPI-CSI 输出，不能直接连接本板。若客户要求即插即用及配套双目算法，应明确回复暂无正式适配方案。
- **相关报错/日志**：⚠️ 上述 USB 双目相机与该开发板没有现成配套例程，兼容性和帧率需实测。

### 能否连接 UVC USB 摄像头，应接哪里
- **客户问题/现象**：客户问USB摄像头支持。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、USB Host、UVC摄像头；型号未选。
- **根因**：需要UVC Host驱动和供电/格式兼容，板载USB口不等于出厂固件已支持全部USB摄像头。
- **回复内容（解决方法）**：原问答建议USB-A接相机、功能跳帽切HOST，参考[乐鑫UVC Host示例](https://github.com/espressif/esp-idf/tree/master/examples/peripherals/usb/host/uvc)编译适配。优先确认UVC/MJPEG输出、分辨率/带宽/功耗；结合本文件已有CH334 Hub限制，初次用直连P4的1号口作对照，不能把经Hub的所有FS/HS组合视为同等支持。按实际跳帽/PCB丝印核对，非Module-DEV-KIT名称不混同。
- **相关报错/日志**：🔍 驱动开发路径，未指定或实测相机，不能承诺任意USB相机即插即用。来源：2026-11-05《确认 USB 摄像头支持情况》，本次读取完整原问答；除另注外无本板实测。

### 平板不识别开发板为键鼠，USB-A与Type-C怎样分工
- **客户问题/现象**：实际接法为实体键鼠→USB-A，跳帽HOST；电脑→USB TO UART，平板→USB Type-C，平板未识别HID。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT Rev1.2原图、USB HS Host与FS Device；平板型号/固件未提供。
- **根因**：COM只证明USB-UART工作，跳帽不生成HID描述符/转发固件；原聊天最初猜测电脑采集键鼠，后被客户明确为USB-A实体键鼠，应采用后续纠正。
- **回复内容（解决方法）**：目标链为键鼠→USB-A Host读HID→P4转换报告→USB Type-C Device→平板Host识别键鼠，USB TO UART用于日志/下载。原图Type-C到GPIO24/25，跳帽不切此Type-C数据；本次[乐鑫USB说明](https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32p4/schematic-checklist-esp32p4.html#usb)确认24/25默认Serial/JTAG，单功能时可选给FS OTG，仍需固件正确PHY/Device/HID配置，不能同时让同一PHY通路承担冲突功能。先最小HID Device枚举测试，再加USB-A键鼠Host转发；电脑检查是否出现HID/仅COM/未知设备，平板用普通鼠标确认OTG与数据线。读取Host示例或接线本身不够，亦非让电脑透传识别平板。
- **相关报错/日志**：状态：待固件名称、板端枚举日志、平板型号与测试结果；未证明本板双端转发已跑通。来源：2026-11-05《排查平板 USB OTG 识别》，本次读取完整原问答；除另注外无本板实测。

## 其他问题

### 语音播报时能否按键、唤醒词或直接插话打断
- **客户问题/现象**：询问说话打断类型及是否需增加硬件。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、小智、AEC。
- **根因**：按键中止、唤醒词检测和无唤醒词插话是不同软件能力；有麦克风不代表已启用AEC实时对话。
- **回复内容（解决方法）**：先评估固件绑定BOOT键中止和板载麦唤醒词检测；直接插话需正确的播放参考、AEC和实时对话管线，可能需补音频前端或服务端支持。逐项核对固件版本配置，不能承诺出厂即支持任意插话。[小智源码](https://github.com/78/xiaozhi-esp32/blob/main/main/application.cc)。
- **相关报错/日志**：⚠️ 未验证本板端侧AEC，服务端方案状态也随版本变化。

### 开机闪几次后恢复正常的软件排查顺序
- **客户问题/现象**：DEV-KIT-C开机闪烁几次，随后显示正常。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT-C、LCD/LVGL。
- **根因**：可能是背光早开、重复初始化、首帧缓冲或启动带宽问题；也需排除供电/反复复位。
- **回复内容（解决方法）**：先试关闭背光→初始化屏幕→清空缓冲并完成首帧→开背光；检查BSP/应用是否重复初始化及控制背光。花屏色块另查像素格式、DMA和缓冲同步；关闭摄像头/解码任务对照，记录启动日志并比较原厂与自编固件。
- **相关报错/日志**：⚠️ 未确认固件/闪烁形态，无实测修复。

### USB 设备直连 1 号口正常，经 CH334 HUB 的 2～4 号口无法识别
- **客户问题/现象**：扫码枪或其他USB设备接1号正常，2～4号无响应；Rev1.2客户接HOST时1号键盘亮，切DEVICE后各口键盘灯均灭。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT；相同结构也见 ESP32-P4-Module-DEV-KIT-A。
- **根因**：1 号口直连 ESP32-P4，2～4 号口经 CH334 HUB；固件可能未开启 HUB，或 FS/LS 设备触发当前 HS Host 经 HUB 不支持 TT 的限制。
- **回复内容（解决方法）**：断电后按版本选择数据通路：Rev1.1接DEVICE=1号直连、HOST=2～4号HUB；Rev1.2相反，HOST=1号、DEVICE=2～4号。DEVICE丝印在此表示选择HUB通路，软件仍运行Host，不是改成Device。四口数据不能同时全启用。再启用 `CONFIG_USB_HOST_HUBS_SUPPORTED=y` 并加载设备类驱动。若日志提示 FS/LS Transaction Translator 不支持，暂用 1 号直连接口，支持相应新版栈时可评估fsls_only=true，不能把TT限制泛化为永久不支持。先测VBUS，再查CH334枚举、HID类驱动和设备速度；键盘灯灭不等于无5V，不能直接判定CH334损坏。[官方版本跳线说明](https://docs.waveshare.net/ESP32-P4-WIFI6-DEV-KIT/FAQ/)、[Host限制](https://docs.espressif.com/projects/esp-usb/en/latest/esp32p4/usb_host.html)。
  增量补充（2026-10-31整理）：2026-10-27咨询同一HUB混合FS/HS：当时官方栈未支持TT/Split Transaction，不能保证HS主机经HS HUB同时按各自速率使用FS/HS外设，换外置HUB也不解决栈限制；可用全HS组合，或在支持的栈版本设置fsls_only使总线降速，但不能保留HS。未实板混合测试，按具体组件版本复核。[Host限制](https://docs.espressif.com/projects/esp-usb/en/latest/esp32p4/usb_host.html)。
  本周支持汇总补充（2026-10-31入库）：周报再次记录同一HUB混用FS/HS时回复“不支持”。该回复仅补作支持记录，沿用本条对当时官方栈TT/Split Transaction的版本限定，不表述为芯片/HUB永久硬件不支持；无新的混合速率实板结果，不新增重复技术结论。
- **相关报错/日志**：`Connected device is FS, transaction translator (TT) is not supported`；无日志时需补充设备类型与ESP-IDF版本；2026-10-21补充客户Rev1.2反馈，尚未确认最终根因。

### 电脑只识别烧录串口，识别不到 USB JTAG/Serial Debug Unit
- **客户问题/现象**：设备管理器只有 CH343 烧录 COM 口，没有 P4 内置 JTAG 通道。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：CH343 对应 `USB_TO_UART`；原生 USB-JTAG 应从 `USB1` 枚举，两者不是同一路。
- **回复内容（解决方法）**：只接 `USB1`，换已验证的数据线；按住 BOOT、短按 RST 后松开 BOOT，再查 `USB\\VID_303A*`。无枚举则排查线材、USB 口和 D+/D-；有黄色感叹号则安装 Espressif 驱动；仅 CDC 无 JTAG 时检查 `DIS_USB_JTAG` eFuse。正常固件占用 GPIO24/25、关闭 USB Serial/JTAG 或进入休眠，也会使设备消失。
- **相关报错/日志**：`Get-PnpDevice -PresentOnly | Where-Object InstanceId -like 'USB\\VID_303A*'`。

### 每次复位都进入 ROM 下载模式
- **客户问题/现象**：板子没有正常启动日志，复位后一直停在下载模式。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：复位瞬间 GPIO35/BOOT 被拉低，可能是 BOOT 键卡住、GPIO35 短路或串口工具持续控制 DTR/RTS。
- **回复内容（解决方法）**：关闭串口监视器和烧录工具；断电重上电，不按 BOOT，只按一次 RST；关闭串口软件的 DTR/RTS 自动控制。仍复现时断电检查 BOOT 键、GPIO35 对地短路，并只用非 UART USB 口供电测试。完全断开 UART 后仍为低电平则建议返修。
- **相关报错/日志**：`boot:0x307 (DOWNLOAD(USB/UART0/SPI))`。

### 视频服务器已联网但网页打不开
- **客户问题/现象**：换供电后网页无法访问，但日志显示 Wi-Fi 已获取 IP。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT。
- **根因**：DHCP 地址变化、电脑不在同一热点、端口 80 未启动、浏览器误用 HTTPS，或充电宝供电不稳。
- **回复内容（解决方法）**：电脑连接同一热点并访问日志中的新地址，例如 `http://10.14.239.147/`；确认后续出现 `Camera web server starts`。用 `ping` 和 `Test-NetConnection <IP> -Port 80` 区分网络与服务器问题，必要时关闭 VPN/代理并先用稳定烧录线供电。
- **相关报错/日志**：`Missing SDIO MODE TLV; Continue anyway` 在已拿到 IP 时通常不是本故障根因。

### P4 芯片从 v1 换成 v3 时需补焊 R8
- **客户问题/现象**：询问 ESP32-P4-WIFI6-DEV-KIT 从 v1.x 更换 v3.x 芯片后应在哪里补焊电阻，以及该器件的作用。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、ESP32-P4 rev v1.x/v3.x。
- **根因**：v1.0/v1.3 的 P4 封装 54 脚为 NC，v3.0 以后改为 `VDD_HP_1`。本板预留 R8，位于主芯片 U8 右侧、靠近 C55 和 R106/R105，用于把第 54 脚连接到约 1.2V 的 `ESP_VDD_HP` 核心电源轨。
- **回复内容（解决方法）**：在 R8 焊盘正规安装 0Ω电阻，使 `U8-54 VDD_HP_1 → R8 → ESP_VDD_HP`；工程验证可临时短接，量产应按 BOM 上件。R8 是电源跳线，不是限流、分压或上拉，绝对不能接 3.3V。不补焊可能导致无法启动、随机重启或高负载死机。v1→v3 还应复核本板 DCDC 反馈网络 `R32/R35/C27` 及其他最新版参考设计要求。
- **相关报错/日志**：🔍 位号和位置基于当前公开原理图及 PCB 位号图；操作前应确认实际硬件版本。

## 待核型号的关联案例

- [[ESP32-P4-通用问题#4-lane MIPI 屏能否改为 2-lane 使用]]
- [[ESP32-P4-通用问题#第三方 GT911 触摸是否属于已适配支持]]

上述原汇总的产品栏为WIFI6-DEV-KIT，依据栏为Module-DEV-KIT-C；未统一认定为同一板型。

### 烧写 eFuse 和 Flash 加密后能否补救，换主控是否能恢复
- **客户问题/现象**：客户说已烧eFuse并启用加密，询问补救及更换主控。
- **涉及产品/型号**：ESP32-P4-WIFI6-DEV-KIT、P4 eFuse、Flash Encryption、Secure Boot。
- **根因**：eFuse已写位不可通过清空Flash撤销；安全配置生效不等于一定无法恢复，恢复路径取决于下载权限、密钥/签名、备份和可用OTA。
- **回复内容（解决方法）**：先保留Flash与现有证据，暂停全擦/新增eFuse操作；收集版本、配置/烧录过程、完整启动错误以及已有只读安全信息，只询问是否持有密钥，不收取密钥。开发/量产加密模式按实际计数和保护状态分别评估；可用路径可能是匹配密钥的预加密固件、受信任签名固件或现有OTA，不能承诺任一条件均能恢复。若确实没有可用恢复入口，可评估更换全新、未配置安全位且型号/板级供电兼容的P4或整个模组；仅换Flash不能清除P4 eFuse。更换后应准备适合新芯片/密钥状态的镜像，原加密Flash内容不能默认沿用，也先审核原工程自动启用安全位的配置。焊接/量产流程需工程师评估，不以重刷保证恢复。
- **相关报错/日志**：状态：恢复与更换方案尚未验证；无实际eFuse状态、密钥是否保留及成功恢复反馈。来源：2026-11-06《排查 P4 开发板 eFuse 加密补救》，本次读取完整原问答；除另注外无本板实测。
