---
产品: ESP32-P4-WIFI6-Touch-LCD-10.1
tags: [FAE, ESP32-P4, OTA, ESP32-C6, 固件, 视频]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-10.1.htm
---

# ESP32-P4-WIFI6-Touch-LCD-10.1

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-X/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-X/Product-Overview.md`。核对日期：2026-10-31。

### 怎样单独调节屏幕背光亮度
- **客户问题/现象**：希望调节背光而不影响其他功能。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、GPIO26、AP3032。
- **根因**：当时图纸GPIO26经LCD_BL_PWM/Q6控制背光反馈；GPIO23的BL_EN与TP_RST共用，不能用它做日常调光。
- **回复内容（解决方法）**：使用对应官方BSP，屏初始化后调用bsp_display_brightness_set(0～100)，例如50为50%；单独初始化时先bsp_display_brightness_init。所查驱动GPIO26、5kHz、10bit，独立PWM时避免与BSP重复占通道。[源码](https://github.com/waveshareteam/Waveshare-ESP32-components/blob/master/bsp/esp32_p4_wifi6_touch_lcd_x/esp32_p4_wifi6_touch_lcd_x.c)。
- **相关报错/日志**：🔍 图纸/源码核查，未实板测试；不同BSP版本按实际接口确认。

### OTG 口默认无 5V，如何连接扫码枪
- **客户问题/现象**：希望通过OTG口连接USB扫码枪，但接口没有VBUS供电，询问改电路或使用外部供电扫码枪的方法。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、USB OTG、USB HID/CDC扫码枪。
- **根因**：OTG Type-C的`USB1_5V`经Q3/U11用于“OTG口向板内供电”，不会把板内VCC_5V反向送回OTG；CC1/CC2又采用5.1kΩ下拉，表现为受电端。仅靠软件无法开启5V VBUS，直接短接VCC_5V与USB1_5V还会带来反灌和多电源争流风险。
- **回复内容（解决方法）**：优先使用真正自供电的扫码枪或外部供电OTG Y线：开发板只连接D+/D-/GND并与扫码枪共地，开发板OTG的VBUS断开或隔离；若扫码枪仍需检测VBUS，则仅在扫码枪侧提供5V，并通过反向阻断高边开关与开发板隔离。板级改造时可从稳定VCC_5V或独立5V经保险/限流、过流短路保护及反向阻断开关送到接口板J1-1/H1 VBUS，端口附近加去耦，数据线保持不变；仅电池供电时还需电池到5V升压。先空载测约5V，再以约500mA验证不低于4.75V，最后测试扫码枪枚举；HID模式用USB HID Host，CDC模式需CDC-ACM Host驱动。
- **相关报错/日志**：⚠️ 不要直接短接`VCC_5V`与`USB1_5V`，不要并联OTG外部5V、UART口5V和其他5V源；部分自供电扫码枪仍需要VBUS存在才会枚举。

### 可以通过什么接口扩展 RJ45 有线网口
- **客户问题/现象**：希望为已有 10.1 英寸一体屏增加 RJ45，插网线上网。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、SPI W5500、USB OTG 网卡。
- **根因**：该一体屏没有板载 RJ45/完整以太网物理通路，不能用无源转接头直接得到网口。外扩网络需要匹配控制器、驱动、GPIO 或 USB Host，以及外设供电。
- **回复内容（解决方法）**：可评估经空闲 GPIO/SPI 接带 RJ45 的 W5500 模块，并加入 ESP-IDF Ethernet 驱动；实际 SPI、CS/INT 等引脚与屏幕、SD/C6 占用需先复核。USB OTG 网卡方案需确认具体芯片的 Host 驱动、USB 枚举和 VBUS 供电，普通电脑用网卡不保证可用。若要求少量开发即可稳定有线联网，优先选带 RJ45 的 P4 主板配 10.1 英寸屏；已有一体屏扩网属于二次开发。[ESP-IDF Ethernet](https://docs.espressif.com/projects/esp-idf/en/v5.4.2/esp32p4/api-reference/network/esp_eth.html)、[本板接口](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-X/)。
- **相关报错/日志**：⚠️ W5500 具体接线、模块兼容及 USB 网卡驱动尚未核实，未实测。来源：2026-10-08 客户问答“查询P4-10寸屏网口扩展”。

### 能否充电和显示电量百分比
- **客户问题/现象**：当前界面不能显示电量或软件充电状态，询问硬件是否支持电源管理、是否需要自行编写代码。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、ETA6003Q3Q、GPIO20/ADC1_CH4。
- **根因**：板载ETA6003Q3Q支持单节锂电池充电与电源路径管理，硬件会自动充电；BAT经200kΩ/100kΩ分压接GPIO20，可由ADC读取电池电压。但板上没有专用电量计芯片，`CHG_STAT`主要接指示灯而未直接连接P4 GPIO，因此软件不能直接读取充电状态，电量百分比也需自行估算。
- **回复内容（解决方法）**：使用单节3.7V、满充4.2V锂电池。程序对GPIO20做ADC校准、平均滤波，按 `电池电压≈ADC电压×3` 计算，再结合实际电池放电曲线查表换算百分比，不能简单线性映射。是否正在充电可直接看板载Charge灯；若必须在屏幕显示充电图标，可把开漏`CHG_STAT`经3.3V上拉引到空闲GPIO。需要更准确剩余容量时增加MAX17048、LC709203F等电量计。
- **相关报错/日志**：🔍 官方示例没有独立电量显示功能；仅用电压换算只能得到近似电量。

### TF 卡文件能否通过 Type-C 像 U 盘一样导出，是否支持 FAT32
- **客户问题/现象**：询问 10.1 英寸 ESP32-P4 平板是否能插 TF 卡，能否通过 Type-C 像 U 盘一样导出卡内数据，以及 FAT32 文件系统能否保存文件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、MicroSD/TF、USB OTG Type-C、FAT32。
- **根因**：官方资料确认板载 SDIO 3.0 MicroSD 卡槽及两个不同用途的 Type-C 接口：USB TO UART 用于供电、烧录和调试，USB OTG 是 P4 原生 USB 2.0 HS 数据口。FatFs 可读写 FAT32，但文件系统支持与 USB 文件导出是不同功能；要让电脑识别为移动磁盘，需要固件实现 USB MSC（大容量存储设备）。
- **回复内容（解决方法）**：可以插 TF 卡保存文件，参考微雪 `examples/esp-idf/05_sdmmc`。导出有三种方式：①停止读写、关闭文件并卸载后取卡，用电脑读卡器拷贝；②开发并烧录 USB MSC 固件，把 TF 卡映射为移动磁盘，用支持数据传输的 Type-C 线连接板上 USB OTG 口与电脑，参考乐鑫 `examples/peripherals/usb/device/tusb_msc`，按本板配置 SDMMC 引脚及 LDO4；③结合板载 P4+C6 Wi-Fi 功能实现 HTTP 文件服务器，由电脑浏览器下载，参考乐鑫 `examples/protocols/http_server/file_serving` 并适配本板网络和存储初始化。若客户说的是 FAT32，可作为卡上文件系统使用；能保存文档字节不等于固件具备 Word/Excel/PDF 的显示或编辑能力。USB 导出前应停止板端存储操作、刷新并关闭文件、卸载文件系统，再交给电脑；电脑安全弹出后再交回板端，不能两端同时挂载访问同一文件系统。🔍 上述为官方硬件及软件能力结论，未在本板实测；不能据此承诺出厂固件已内置 U 盘或网页导出功能。
  追问“有没有样例”的补充：微雪 `05_sdmmc` 用于先验证 TF 卡读写；电脑 U 盘导出使用乐鑫 `tusb_msc` 并适配本板，目标选 `esp32p4`，TF 接线为 CLK=GPIO43、CMD=GPIO44、D0～D3=GPIO39～42，内部 LDO 通道设为 4，再从 USB OTG Type-C 接电脑。所用 ESP-IDF 版本若菜单显示 `SDMMC Card LUN`，只需导出卡时启用它、关闭 SPI Flash LUN；当前上游示例也可能采用 `Storage Media Used → SDMMC CARD` 的单存储介质选择，按所用版本 README/Kconfig 配置。4线模式需核对六个引脚及 LDO 配置，先确认卡可挂载再验证 MSC 枚举。🔍 官方参考样例尚未在此 10.1 英寸板上完成编译和实机导出测试，不能视为直接可用的成品固件。
- **相关报错/日志**：无。官方依据（2026-09-30 核对）：https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-X/ （硬件说明第10、15、16项）；https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-X/tree/main/examples/esp-idf/05_sdmmc ；https://docs.espressif.com/projects/esp-idf/en/stable/esp32p4/api-reference/storage/fatfs.html ；https://github.com/espressif/esp-idf/tree/master/examples/peripherals/usb/device/tusb_msc ；https://github.com/espressif/esp-idf/tree/master/examples/protocols/http_server/file_serving 。补充来源：2026-09-30 客户问题聊天“查找 ESP32-P4 10.1 英寸屏资料”的样例追问。

### 能否实现“U盘/TF卡插入即播任意 MP4”
- **客户问题/现象**：希望播放用户随带的 Main/High Profile、720p/1080p MP4，达到车载播放器体验。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：ESP32-P4内置 H.264硬件编码器而非解码器；软件解码只支持受限Profile，720p性能不足。
- **回复内容（解决方法）**：只能支持预先转码、规格受控的 MP4，不能保证“任意格式”。可限定 H.264 Constrained Baseline、较低分辨率和受支持音频，启用双任务/IRAM优化并用PPA缩放。若必须兼容任意720p/1080p Main/High，应改用带 H.264/H.265 VPU、Linux/Android和成熟播放器框架的应用处理器；这属于第三方主控选型和完整播放器开发，不在开发板常规技术支持范围。
- **相关报错/日志**：历史参考性能：1280×720约10fps，640×480约31fps。

### 如何作为 Windows 电脑的扩展屏
- **客户问题/现象**：希望将 ESP32-P4-WIFI6-Touch-LCD-10.1 作为现有电脑屏幕的扩展副屏。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、USB OTG、Windows 10/11。
- **根因**：本板为 ESP32-P4 驱动的 MIPI-DSI 屏，未提供 HDMI/DP 视频输入；作为电脑副屏需板端 USB 扩展屏固件与电脑端 Windows IDD 驱动协同，图像通过 USB OTG 传输。
- **回复内容（解决方法）**：使用微雪 ESP32-P4-WIFI6-Touch-LCD-X 仓库的 `examples/esp-idf/12_usb_extend_screen`，在 ESP-IDF 中选择 esp32p4 与 10.1 英寸屏配置后编译、通过 USB TO UART 口烧录；从乐鑫 USB 扩展屏 `windows_driver` 页面下载并安装签名驱动，用支持数据传输的 Type-C 线将板上 USB OTG 口接电脑，在 Windows 显示设置中选择“扩展这些显示器”并排列屏幕位置。不要把出厂固件当作已具备 USB 扩展屏功能，也不要将 HDMI 线或转接头直接接到该板。🔍 10.1 英寸面板原生 800×1280，实际横屏分辨率、旋转和触摸映射需以所用示例版本与实机测试为准；乐鑫通用示例文档提示其 Windows 驱动暂不支持竖屏。
- **相关报错/日志**：无。参考：https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-X 、https://docs.espressif.com/projects/esp-iot-solution/zh_CN/latest/usb/usb_overview/usb_device_solutions.html 、https://github.com/espressif/esp-iot-solution/blob/master/examples/usb/device/usb_extend_screen/windows_driver/README_cn.md 。

### 能否实现配网与 OTA
- **客户问题/现象**：希望设备首次启动配网，后续在线升级。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：硬件有32MB Flash和C6 Wi-Fi，具备条件；P4主程序OTA与C6协处理器升级是两套流程。
- **回复内容（解决方法）**：推荐第一版采用 SoftAP网页配网 + P4主程序 HTTPS OTA：凭据存NVS，配置 `ota_0/ota_1`双分区，升级后自检并支持回滚。暂不优先 BLE配网，因为 P4+C6 Hosted 架构兼容性更复杂。C6固件升级另行处理。
- **相关报错/日志**：无。

### 怎样把画面投到投影仪，LT8912B 能否直接使用
- **客户问题/现象**：客户希望连接投影仪，提出DSI转HDMI、RGB/DVP备选及1080p约15fps/720p30fps说法。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、候选LT8912B桥接板；投影仪型号未知。
- **根因**：没有原生HDMI或普通Type-C视频输出；MIPI-DSI接口目前连接自带屏，转换方向正确不等于实际桥接模块已匹配。
- **回复内容（解决方法）**：可评估定制MIPI-DSI→HDMI桥接，模块需支持P4的2-lane、连续时钟等模式，核对排线/供电/复位/控制接口并开发初始化与视频时序；不能简单一分二同时接原屏。删除无依据的1080p15fps、720p30fps保证，界面绘制帧率不等于HDMI输入刷新率。[LT8912B简介](https://www.lontiumsemi.com/UploadFiles/2022-03/LT8912B_Brief_R1.3.pdf)的芯片标称能力不证明整套方案通过。备选Wi-Fi传屏需开发板采集压缩→电脑/树莓派接收→HDMI输出，另验协议、延迟和画质。控制投影仪与投画面分开评估。RGB显示输出不能混称摄像头DVP输入；音频需桥接模块实际引出I2S/SPDIF等接口并适配。
- **相关报错/日志**：⚠️ 方案评估，未选定具体模块/投影仪且未实测；不能靠普通USB视频线或蓝牙音频模块投屏。来源：2026-11-02客户问题项目及同目录聊天《说明连接投影仪的方法》，逐条读取用户问题与最终答复；除另注外未本次实机验证。关联《确认是否支持外接 MH-M38》。

### 能否用 MH-M38 把板上声音发给蓝牙音箱
- **客户问题/现象**：客户想外接MH-M38，将开发板声音发送到蓝牙音箱；后续还询问投影仪。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、ESP32-C6、MH-M38。
- **根因**：MH-M38属于音频接收/功放模块，不是客户需要的A2DP Source发射端；板载C6的BLE不提供普通蓝牙音箱所用经典A2DP。
- **回复内容（解决方法）**：应选支持A2DP Source/TX的蓝牙音频发射器：数字路径需I2S输入并适配采样率、时钟和数据格式；模拟路径需从ES8311功放前端取音并匹配电平，不能把两针功放喇叭口直接接AUX。MH-M38可独立接收手机音乐，但不把其功放输出与主板输出并接，L−/R−也不当GND。投影仪还需确认是否支持蓝牙音频接收；发送到音箱的蓝牙功能不代表支持传画面。参考[原问答所用模块说明](https://mantech.co.za/Datasheets/Products/m18-m28-m38-r0.pdf)；具体候选模块须单独核对。
- **相关报错/日志**：⚠️ 尚无选定发射模块/具体接线/固件与实测，不承诺即插即用。来源：2026-11-02客户问题项目及同目录聊天《确认是否支持外接 MH-M38》，逐条读取用户问题与最终答复；除另注外未本次实机验证。

### 是否自带 Modbus，RTU 和 TCP 分别怎样实现
- **客户问题/现象**：客户询问本板是否有Modbus通信功能。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、esp-modbus、UART/RS485、Wi-Fi。
- **根因**：可开发相应协议与出厂已集成协议不同，RTU电气通路与TCP网络通路也不同。
- **回复内容（解决方法）**：原问答按[硬件资料](https://docs.waveshare.com/ESP32-P4-WIFI6-Touch-LCD-X)确认无板载RS485收发器，Modbus RTU需外接3.3V逻辑兼容UART-RS485模块并适配方向/协议；Modbus TCP可通过板载Wi-Fi开发，参考[乐鑫Modbus文档](https://docs.espressif.com/projects/esp-idf/en/v5.5.4/esp32p4/api-reference/protocols/modbus.html)。原核查的官方板级列表未提供专用Modbus例程，不当作所有未来版本均无；主从角色、寄存器表、数据格式与目标PLC/仪表需客户联调。
- **相关报错/日志**：🔍 能力/开发路径说明，无客户设备Modbus联调实测。来源：2026-11-04《确认是否支持 Modbus 通信》，本次逐条读取原聊天；资料/源码分析与实机结果分开记录。

## 其他问题

### Type-C 与 5V 端子分别供电时 PWR 灯均不亮
- **客户问题/现象**：Type-C和所称5V端子分别供电，指示灯均不亮；询问全部基础检查无效后的可能原因。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、LCD-X系列接口子板。
- **根因**：⚠️ 尚未确诊。两种入口都失败优先查公共电源、开关、子板连接和稳压/短路，不能仅凭灯不亮判定P4损坏。PWR电源灯与Charge充电灯含义不同。
- **回复内容（解决方法）**：先确认独立电源开关开启，分清仅屏不亮还是PWR也不亮。每次只接一种源：USB TO UART口用可靠短线/稳定5V，端子按实际丝印确认5V/GND，不能凭口述定位。断电检查子板排线和供电线。测5V/3V3对GND：5V无或跌落查输入/接触/短路；5V正常而3V3无查开关控制与稳压；两轨正常查灯子板和启动日志。排除线材/电源/极性后，才进一步定位连接、开关器件、稳压损坏或后级短路，并建议售后检测。明显发热、异味时立即断电，不再反复试电。[硬件说明](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-X/)。
- **相关报错/日志**：🔍 需背面完整接线图、明确端子位置、电源V/A、板次及输入电流/电压测量。来源：2026-10-22“排查 ESP32-P4 10.1寸屏供电无灯”。

### C6 出厂 ESP-Hosted 版本、DIO/QIO 和恢复固件能否确认
- **客户问题/现象**：询问 C6 出厂版本、Bootloader/App Flash mode、能否提供恢复包。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：历史查询时微雪公开仓库未给出批次对应的 C6 完整恢复包和精确版本，P4端组件版本不能替代C6版本。
- **回复内容（解决方法）**：🔍 先通过 P4 API 或 C6 UART查询实机；拿到固件后分别用 `esptool.py --chip esp32c6 image_info`检查 bootloader与app。向厂家索取 bootloader、partition table、ota data、network_adapter、flash args、SHA-256及安全启动/加密状态。不能把第三方或最新上游镜像当作出厂恢复固件。
- **相关报错/日志**：无。

### 不接 J13 能否更新 C6
- **客户问题/现象**：希望通过 P4/SDIO 更新板载 C6。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：ESP-Hosted 支持 Slave OTA，但依赖 C6 正常启动并支持对应 RPC。
- **回复内容（解决方法）**：可以用 `esp_hosted_slave_ota_begin/write/end/activate` 从 HTTPS、P4 Flash、文件系统或SD卡更新 C6应用分区；这不是救砖通道。C6 bootloader/分区表损坏、旧固件不支持OTA或SDIO无法建立时，仍需 J13 的 IO9/U0RXD/U0TXD。
- **相关报错/日志**：无。

### 如何查询 C6 固件版本
- **客户问题/现象**：希望从 P4 端读取板载 C6 版本。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：需等 ESP-Hosted 连接完成后调用查询API；旧 C6固件可能不实现该RPC。
- **回复内容（解决方法）**：先查 C6自身启动日志中的 `ESP-Hosted-MCU Slave FW version :: X.Y.Z`。P4端可在连接从机后调用 `esp_hosted_get_coprocessor_fwversion()`。若超时，不能把 `0.0.0` 当真实版本，只能查 C6 UART日志或回读 Flash。
- **相关报错/日志**：`Req_GetCoprocessorFwVersion (0x15e)`。

### 查询结果显示 Co-proc 0.0.0 并 RPC 超时
- **客户问题/现象**：SDIO已启动，但读取 C6版本返回失败。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：`0.0.0` 是未上报版本时的占位值；旧 network_adapter固件没有实现新版版本查询RPC，或主从协议不匹配。
- **回复内容（解决方法）**：通信出现 `TRANSPORT_TX_ACTIVE`、`Coprocessor Boot-up` 说明基础SDIO可用；删除版本查询可避免每次约1秒超时。换成 Host端 1.0.2 也无效，因为它仍发送相同的 `0x15e`。要确认版本，应接 J13读 C6日志或回读 Flash；要使API可用则升级为配套固件。
- **相关报错/日志**：`Version mismatch: Host [2.12.0] > Co-proc [0.0.0]`、`Response not received for [0x15e]`。

### 提供的 ESP32-C6_006.bin 是什么版本
- **客户问题/现象**：希望从 C6 完整BIN中解析固件信息。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、ESP32-C6_006.bin。
- **根因**：二进制镜像头与 app description 可提取版本，但不能证明板上已烧录同一文件。
- **回复内容（解决方法）**：历史解析结果：项目 `network_adapter`，应用版本 `release/ng-v1.0.2-330-g83efce6`，ESP-IDF `v5.4-dev-3602-ga97a7b0962`，编译时间 2024-10-18，DIO/80MHz/4MB。只有确认板载C6确实烧过该BIN，才能把它作为当前版本。
- **相关报错/日志**：无。

### 出厂 FactoryOnly 固件应选择哪种 Flash Mode
- **客户问题/现象**：询问 `ESP32-P4-WIFI6-Touch-LCD-10.1-FactoryOnly-260820.bin` 使用 DIO 还是其他模式。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：历史任务直接解析了 Bootloader 与 Factory App 镜像头。
- **回复内容（解决方法）**：该特定文件的 Bootloader（0x2000）和 Factory App（0x200000）模式字节均为 `0x02`，烧录工具 Flash Mode 选择 DIO。文件换版本后应重新解析，不能泛化到所有固件。
- **相关报错/日志**：模式字节 `0x02 → DIO`。
