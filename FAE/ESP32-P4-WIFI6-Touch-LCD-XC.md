---
产品: ESP32-P4-WIFI6-Touch-LCD-XC
tags: [FAE, ESP32-P4, USB, UART, 舵机]
商品链接: https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-XC/
---

# ESP32-P4-WIFI6-Touch-LCD-XC

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-XC、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-XC/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-XC/Product-Overview.md`。核对日期：2026-10-31。

### 40Pin 的 GPIO24/25 USB 对应 UART 几
- **客户问题/现象**：把40Pin标USB的引脚当作TTL串口，希望打印数据。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-XC、USB Serial/JTAG。
- **根因**：GPIO24/25为原生USB D−/D+，不是UART TX/RX，USB差分协议不能直接接USB转TTL的串口侧。
- **回复内容（解决方法）**：正常供电后用合规USB转接将GPIO24接D−、25接D+并接GND；ESP-IDF控制台选择USB Serial/JTAG（CONFIG_ESP_CONSOLE_USB_SERIAL_JTAG=y），printf/日志从新枚举COM输出。若要TTL则另配置UART GPIO；改引脚复用前排除板上连接冲突。[控制台说明](https://docs.espressif.com/projects/esp-idf/en/v5.5.5/esp32p4/api-guides/usb-serial-jtag-console.html)。
- **相关报错/日志**：🔍 接线按XC实板图纸，不能按排针物理编号直接找GPIO。

### 如何从 40Pin GPIO 输出 UART 数据
- **客户问题/现象**：先问单独TX输出，后指定用GPIO37/38。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-XC、UART0/UART1。
- **根因**：UART可经GPIO Matrix配置；GPIO37/38的UART0还与板载USB-UART共用。
- **回复内容（解决方法）**：指定方案用UART0 TX=37/RX=38，TX接对方RX、RX接对方TX并共地，3.3V逻辑且串口参数一致；注意板载桥接和日志干扰。需独立业务串口可评估UART1 TX=GPIO4等空闲脚，uart_set_pin和uart_write_bytes显式指定UART，不必改变全局控制台。仅发送可只接TX/GND。
- **相关报错/日志**：⚠️ 推荐GPIO4需查程序占用；标注GPIO4不是排针第4脚。

### 一个 PWM 舵机怎样接线与配置
- **客户问题/现象**：希望通过排针控制一只普通PWM舵机。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-XC、LEDC。
- **根因**：GPIO只提供控制信号，不能给舵机供动力；LEDC若与背光共用定时器会互相影响。
- **回复内容（解决方法）**：可评估空闲GPIO4作信号，舵机按规格独立供电并与板共地，确认支持3.3V控制。LEDC选未占用定时器/通道；常见50Hz、1500µs中位，先小幅调节，不直接套500～2500µs全行程。供电覆盖启动/堵转电流。[舵机说明](https://docs.espressif.com/projects/esp-iot-solution/en/latest/motor/servo.html)。
- **相关报错/日志**：⚠️ 舵机型号和行程未确认，未实机测试。

### 06_I2SCodec 示例怎样调整扬声器音量
- **客户问题/现象**：客户询问此示例声音如何调大小。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-XC系列、06_I2SCodec、ES8311；具体屏尺寸未明确。
- **根因**：该示例播放音量由EXAMPLE_VOICE_VOLUME传入ES8311设置函数，不是Mic录音增益。
- **回复内容（解决方法）**：原核查[Kconfig.projbuild](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-XC/blob/main/examples/esp-idf/06_I2SCodec/main/Kconfig.projbuild)参数范围0～100、默认60；idf.py menuconfig→Example Configuration→Voice volume调整后编译烧录。也可在main/example_config.h调整EXAMPLE_VOICE_VOLUME，初始化通过es8311_voice_volume_set(es_handle,EXAMPLE_VOICE_VOLUME,NULL)设置。优先保留配置菜单路径，避免硬编码使菜单失效；以实际工程版本为准。
- **相关报错/日志**：🔍 源码设置入口已在原问答核对，未上板对比声压/失真，不代表更大数值必然更清晰。来源：2026-11-03《调整 I2SCodec 音量》，本次逐条读取原聊天；资料/源码分析与实机结果分开记录。

## 其他问题

### Host BT Support Disabled 是什么，IDF 6.0.2 如何开启 BLE
- **客户问题/现象**：启动日志显示Host BT Support Disabled，随后核对04_wifistation并询问如何开启蓝牙。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-XC、ESP-IDF 6.0.2、ESP-Hosted 2.12.12、NimBLE。
- **根因**：hci_stub_drv日志说明P4侧编译了蓝牙占位驱动，未开启Hosted HCI，并非硬件故障。当前wifistation仅有Wi-Fi业务，开启配置也不会自动产生BLE广播。
- **回复内容（解决方法）**：按该版本在menuconfig启用Bluetooth、Host=NimBLE、Controller=Disabled（仅关闭P4本地控制器）；取消NimBLE UART transport，再开启Hosted NimBLE与VHCI。关键项：CONFIG_BT_ENABLED=y、CONFIG_BT_NIMBLE_ENABLED=y、CONFIG_BT_CONTROLLER_DISABLED=y、CONFIG_ESP_HOSTED_ENABLE_BT_NIMBLE=y、CONFIG_ESP_HOSTED_NIMBLE_HCI_VHCI=y，CONFIG_BT_NIMBLE_TRANSPORT_UART不启用。参考所用组件内examples/host_nimble_bleprph_host_only_vhci：Hosted连接→esp_hosted_bt_controller_init/enable→NimBLE服务/同步回调→启动广播，C6固件也须支持Hosted蓝牙。旧1.0.2的CONFIG_ESP_ENABLE_BT/ESP_HCI_VHCI名称不能直接套入2.12.12。Wi-Fi账号在项目sdkconfig配置后需重新编译烧录，不在知识库保存密码。参考[所用Hosted 2.12.12](https://components.espressif.com/components/espressif/esp_hosted/versions/2.12.12/readme)内同名VHCI示例，以该版本Kconfig及API为准。
- **相关报错/日志**：`hci_stub_drv: Host BT Support: Disabled`；启用后可见`vhci_drv: Host BT Support: Enabled`，但仍需BLE扫描工具及实板验证广播。来源：2026-10-21“蓝牙配置”；未完成蓝牙编译/实测确认。
