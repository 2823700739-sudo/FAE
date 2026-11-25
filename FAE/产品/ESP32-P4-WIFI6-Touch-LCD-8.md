---
产品: ESP32-P4-WIFI6-Touch-LCD-8
tags: [FAE, ESP32-P4, LVGL, RS485, Modbus, SD卡]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-8.htm
---

# ESP32-P4-WIFI6-Touch-LCD-8

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-8、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-X/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-X/Product-Overview.md`。核对日期：2026-10-31。

### 两路 RS485 的 TX/RX 与 DE/RE 可以怎样分配
- **客户问题/现象**：一路连接 PLC Modbus，另一路连接 485 摄像头，希望推荐方向控制 GPIO。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-8、UART1/UART2、RS485 收发器、40Pin J8。
- **根因**：按所查 Touch-LCD-X 图纸，推荐六个 GPIO 未与屏幕、触摸、SD/C6 通路重叠；板侧 UART 逻辑仍需外接 RS485 物理层收发器。串口摄像头 485 接口可能只有控制功能，须核对协议是否传图。
- **回复内容（解决方法）**：可评估 PLC 用 UART1：TX=GPIO21（J8-15）、RX=GPIO22（J8-17）、方向=GPIO28（J8-16）；摄像头用 UART2：TX=GPIO30（J8-20）、RX=GPIO31（J8-22）、方向=GPIO29（J8-18）。J8 数字为排针物理脚号。每路采用 3.3V 逻辑兼容收发器，普通半双工 DE 与 /RE 并接，方向 GPIO 映射 UART RTS、关闭硬件流控并启用 `UART_MODE_RS485_HALF_DUPLEX`；自动方向模块按其说明处理。保留 C6 控制、BOOT 和 UART0 日志脚，接线前按实物版次和其他占用复核。[Touch-LCD-X 原理图](https://files.waveshare.com/wiki/ESP32-P4-WIFI6-Touch-LCD-X/ESP32-P4-WIFI6-Touch-LCD-X-Schematic.pdf)、[ESP-IDF UART/RS485](https://docs.espressif.com/projects/esp-idf/en/v5.5.5/esp32p4/api-reference/peripherals/uart.html)。
- **相关报错/日志**：⚠️ 上述为图纸推导的候选接线，未在种养柜或具体 485 摄像头上验证。来源：2026-10-08 种养柜客户问答。

## 其他问题

### 种养柜应从哪个例程开始，ESP-IDF 和屏幕配置如何选择
- **客户问题/现象**：种养柜需要 LVGL 触摸界面、两路 RS485、SD 卡素材和 Wi-Fi，询问基础工程、ESP-IDF 版本及 PSRAM/menuconfig 配置。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-8、Touch-LCD-X、LVGL 9、ESP32-C6。
- **根因**：官方 `08_lvgl_demo_v9` 已包含屏幕、触摸、背光和 LVGL 基础，但没有完整 PLC/摄像头业务。2026-10-08 当时源码核查的例程默认 10.1 英寸屏、P4 Rev3.x，屏型和 revision 必须匹配实板；该配置快照不能代替所有仓库版本。
- **回复内容（解决方法）**：以 [08_lvgl_demo_v9](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-X/tree/main/examples/esp-idf/08_lvgl_demo_v9) 为基础，替换 demo 界面，逐项加入同仓库 `04_wifistation`、`05_sdmmc` 和 ESP-Modbus/UART 任务。当时建议固定 ESP-IDF v5.5.5，依赖快照为 LVGL 9.5.0、BSP ^2.0.3，并保存成功构建的 lock。menuconfig 的 BSP Display 选 8 英寸屏，初期保留 RGB565、PSRAM 200MHz 等已有配置；核对 32MB Flash 与芯片 revision，旧 v1.3 使用仓库 rev1_3 配置。素材接入 LVGL 文件系统/图片解码器，中文界面增加包含所需汉字的字体；Wi-Fi 主机组件需与 C6 固件配套。[例程配置说明](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-X/blob/main/examples/esp-idf/08_lvgl_demo_v9/README_ZH.md)、[芯片版本配置](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-X/blob/main/docs/silicon-revisions.md)。
- **相关报错/日志**：🔍 版本和默认参数为 2026-10-08 客户答复中的源码核查快照，后续使用前核对所选 tag/commit；未实测整机集成。

### 图片和问答文本放 SD 卡时是否需要改 Flash 分区
- **客户问题/现象**：计划存放图片、问答文本和项目配置，询问是否修改 Flash 分区表。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-8、SD/FAT、SPIFFS、NVS、OTA。
- **根因**：SD 文件系统与板载 Flash 分区表独立；当时核查的 LVGL 例程已有 factory 8MB 和 storage SPIFFS 7MB，但不存在 OTA 应用槽。分区存在不代表素材已经装入或挂载。
- **回复内容（解决方法）**：图片、问答库和日志主要放 SD 卡时，不必仅为素材修改 Flash 分区；联网参数和业务设置放 NVS，必要启动资源放 Flash。少量资源也可使用现有 SPIFFS storage，需自行挂载和写入。应用超出 8MB、Flash 素材不够或增加 OTA 时再规划分区；OTA 应增加配套应用槽与元数据，并保留资源/配置空间。[当时例程分区表](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-X/blob/main/examples/esp-idf/08_lvgl_demo_v9/partitions.csv)。
- **相关报错/日志**：🔍 factory/storage 数值限定当时源码，先核对实际 partitions.csv；来源：2026-10-08 种养柜客户问答。
