---
产品: ESP32-P4-Pico
tags: [FAE, ESP32-P4, USB, 供电]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Pico.htm
---

# ESP32-P4-Pico

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### Type-C 接口是不是 USB 3.0
- **客户问题/现象**：看到Type-C外形，询问是否支持USB3.0。
- **涉及产品/型号**：ESP32-P4-Pico。
- **根因**：Type-C是连接器外形，不代表协议版本；此口用于供电/UART下载调试。
- **回复内容（解决方法）**：不是USB3.0。另一个4Pin USB OTG接口才是USB数据口，规格为USB2.0 High-Speed，理论480Mbps；换USB3.0线不会提升协议速度。
- **相关报错/日志**：无。

### 4Pin USB 给约 300mA 摄像头供电是否足够
- **客户问题/现象**：担心 VBUS不足，想从排针5V再拉一根线给摄像头。
- **涉及产品/型号**：ESP32-P4-Pico。
- **根因**：4Pin VBUS与排针 VSYS/5V本来就是同一 `VCC_5V`，另拉线不会增加上游电源能力。
- **回复内容（解决方法）**：300mA摄像头大概率可用，但官方未给接口独立限流值。建议整板使用稳定5V/2A供电；普通电脑USB2.0的500mA总预算可能不足。独立给摄像头供电时必须断开原USB线中的VBUS，只保留D+/D-/GND并共地，不能并联两个5V源。启动和出图时测摄像头端VBUS，尽量保持4.75V以上。
- **相关报错/日志**：供电不足表现为复位、枚举失败或图像中断。

### OTG 接口在哪里
- **客户问题/现象**：找不到板上的USB OTG接口。
- **涉及产品/型号**：ESP32-P4-Pico。
- **根因**：OTG不是Type-C，而是板上丝印 `USB` 的 MX1.25 4Pin插座。
- **回复内容（解决方法）**：使用与本板针序匹配的 MX1.25 4Pin 转 USB 线；基础板可能不附线，按套餐核对。Type-C 接 CH343P，承担供电/UART 烧录日志；自定义 CDC/HID/MSC 等 USB Device 固件应通过丝印 USB 的 4Pin HS OTG 数据接口连接电脑，参考 ESP-IDF/TinyUSB 并处理相应 VBUS。[官方接口资料](https://docs.waveshare.net/ESP32-P4-Pico/)。
- **相关报错/日志**：无。

### USB 能否工作在 Host 模式
- **客户问题/现象**：想接U盘、键鼠或摄像头。
- **涉及产品/型号**：ESP32-P4-Pico。
- **根因**：4Pin接口连接P4 USB OTG 2.0 HS；Type-C连接CH343P，不是Host口。
- **回复内容（解决方法）**：4Pin口可配置USB Host，需在ESP-IDF启用Host栈及对应类驱动，并保证5V VBUS。高功耗设备优先使用有源Hub，防止供电不足或反灌。
- **相关报错/日志**：无。

### 是否集成 C6并支持 Wi-Fi 6
- **客户问题/现象**：询问ESP32-P4-Pico是否有Wi-Fi 6，以及板上是否集成ESP32-C6。
- **涉及产品/型号**：ESP32-P4-Pico、ESP32-P4NRW32、ESP32-C6。
- **根因**：该板只板载ESP32-P4NRW32，没有集成ESP32-C6；ESP32-P4本身不带Wi-Fi或蓝牙射频。
- **回复内容（解决方法）**：ESP32-P4-Pico本身不支持Wi-Fi 6或蓝牙。需要板载无线协处理器时应选择P4+C6组合产品，例如ESP32-P4-WIFI6或ESP32-P4-Module系列，并按对应ESP-Hosted架构开发。
- **相关报错/日志**：无。

### 22Pin Display 口能否直接接第三方屏
- **客户问题/现象**：Pico-KIT-B客户询问是否只能用套餐屏。
- **涉及产品/型号**：ESP32-P4-Pico-KIT-B、2-lane MIPI-DSI。
- **根因**：不限定套餐，但22Pin只是连接器针数，不等于脚位/驱动兼容。
- **回复内容（解决方法）**：第三方适配需核对DSI lane、针序/间距/触点、电源/背光及初始化时序和触摸驱动。官方可用5/7/8/10.1英寸方案可先参考，未有据此保证开箱即用的第三方型号；先索取候选屏规格再判断。[板卡资料](https://docs.waveshare.net/ESP32-P4-Pico/)。
- **相关报错/日志**：🔍 尚无第三方屏具体型号或实测，不承诺仅换排线即用。

## 其他问题

### 如何证明发货芯片 revision 和安全 eFuse 状态
- **客户问题/现象**：客户要求确认发货是否为 P4 v3.2、提供 esptool 识别结果，并确认全新芯片、未写安全 eFuse 及配套资料。
- **涉及产品/型号**：ESP32-P4-Pico、ESP32-P4 revision、Secure Boot、Flash Encryption、eFuse。
- **根因**：供应批次信息、芯片实际 revision、采购来源和 eFuse 安全状态是不同证据。用户在 2026-10-08 表示当前按 v3.2 出货，但未提供实板识别日志；公开商品信息不能证明某块芯片从未写入密钥。
- **回复内容（解决方法）**：从当前批次 Type-C/UART 连接执行 `python -m esptool --chip esp32p4 --port COMx read-mac`，附实际输出中的 revision。使用只读 `get-security-info` 和 `python -m espefuse --chip esp32p4 --port COMx summary` 核对 Secure Boot、Flash Encryption、下载限制、密钥块和读写保护；安全功能未启用不足以证明密钥从未写入。全新/非回收需采购生产记录支持，不得代填实测结果。资料可提供 [原理图](https://files.waveshare.com/wiki/ESP32-P4-Pico/ESP32-P4-Pico-datasheet.pdf)、[Platform ESP-IDF 示例](https://github.com/waveshareteam/ESP32-P4-Platform)、[eFuse summary 说明](https://docs.espressif.com/projects/esptool/en/latest/esp32p4/espefuse/summary-cmd.html)。
- **相关报错/日志**：🔍 v3.2 为用户当时批次说明，不是所有未来批次保证；尚无本批次 esptool/eFuse 结果。来源：2026-10-08 客户问答“核实 ESP32-P4-Pico 芯片与 USB”。

### GPIO47/48 拉高后仅约 1.2 V

- **客户问题/现象**：ESP32-P4-Pico 的 GPIO47/48 配置输出高电平后，实测只有约 1.2 V，原理图上看不出受何处影响。
- **涉及产品/型号**：ESP32-P4-Pico、ESP32-P4NRW32、GPIO47、GPIO48、VDDPST_5、ESP_LDO_VO4。
- **根因**：官方Pico原理图中，U6的`ESP_LDO_VO4`（74脚）回接到`VDDPST_5`（85脚），为GPIO39～48输出级和板载MicroSD信号上拉供电，因此GPIO47/48高电平受VO4实际电压限制。客户进一步实测VO4也约1.2V，说明问题位于整个LDO4/VDDPST_5电源域，而不只是两个GPIO配置；但仍需区分软件未正确配置、同域负载拉低或硬件异常。U3 MP1605把`ESP_3V3`降到约1.2V形成`ESP_VDD_HP`核心电源，它与`ESP_LDO_VO4`是不同网络，不能因读数相近就认定两者短接。
- **回复内容（解决方法）**：先断开GPIO47/48外设并取下MicroSD，用最小固件通过`esp_ldo_acquire_channel()`配置LDO通道4、`voltage_mv=3300`，延时约100ms后把GPIO47/48设为推挽输出并保持常高；分别测ESP_3V3、C31/C32非接地端VO4及两GPIO。若VO4恢复3.3V则继续排查原工程未初始化LDO4或外设复用；若最小固件下VO4仍约1.2V，检查TF卡与外接负载、板上短路或LDO域硬件。VDDPST_5工作范围为1.65～3.6V，持续1.2V不是可靠高电平，禁止用外部3.3V直接硬灌该GPIO域。
- **相关报错/日志**：GPIO47/48与`ESP_LDO_VO4`均实测约1.2V；⚠️ 最小固件尚未在客户板上完成对照测试，具体是软件配置、负载还是硬件故障仍待确认。
