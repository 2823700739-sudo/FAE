---
产品: ESP32-P4-WIFI6-POE-ETH
tags: [FAE, ESP32-P4, ESP32-C6, BLE, ESP-Hosted]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-POE-ETH.htm
---

# ESP32-P4-WIFI6-POE-ETH

### C6 Hosted 声明支持 BLE HCI，但蓝牙控制器初始化无确认
- **客户问题/现象**：Wi-Fi 可用且 C6 上报 HCI 能力，但 P4 启动蓝牙控制器失败；客户希望确认出厂 Hosted 版本并寻找更可靠的蓝牙实现方案。
- **涉及产品/型号**：ESP32-P4-WIFI6-POE-ETH、ESP32-C6。
- **根因**：ESP32-P4 本身没有 Wi-Fi/蓝牙射频；P4 运行蓝牙 Host，C6 运行 Controller 并负责射频，双方通过 ESP-Hosted/SDIO 传输 HCI。P4 与 C6 的 ESP-Hosted 版本、RPC 协议和蓝牙配置不匹配时，即使 C6 声明 HCI 能力也可能初始化失败。
- **回复内容（解决方法）**：优先使用经过该型号实机验证的成套 P4 示例与 C6 固件：P4 启用 Hosted Bluetooth 和 NimBLE、关闭本地 Controller；C6 使用同版本 ESP-Hosted Slave，启用 BLE Controller 与 Hosted HCI over SDIO。不要把面向 ESP32-S3 单芯片的蓝牙工程直接套到 P4，也不要只烧 P4 或随意打开两个宏。通过 C6 UART 启动日志查询 `ESP-Hosted-MCU Slave FW version :: X.Y.Z`，再与 P4 的 `dependencies.lock` 对照；用户历史实测过一块 C6 为 `0.0.6`，但版本号本身不能证明兼容。若不能修改 C6，只有外接独立蓝牙模块；另一方案是把完整蓝牙业务运行在 C6 上，由 P4 经 UART/自定义协议控制，但开发量更大。
- **相关报错/日志**：历史实测：`I (...) fg_mcu_slave: ESP-Hosted-MCU Slave FW version :: 0.0.6`；应关注 C6 能力列表是否包含 `HCI over SDIO` 和 `BLE only`。

### MP1658 与整板 5V 电源轨的电流上限
- **客户问题/现象**：询问 MP1658GTF-Z 的最大输入电流、整板 `VCC_5V` 电源轨上限，以及能否从 5V 端子或 USB-A 给 4G 模块供电。
- **涉及产品/型号**：ESP32-P4-WIFI6-POE-ETH、4G 蜂窝模块。
- **根因**：原理图中的 `3A MAX` 是 MP1658 将 5V 降为 3.3V 的输出能力，不是 MP1658 固定输入电流，也不是整板 5V 母线的统一限流值。5V 端子直接连接 `VCC_5V`，可用电流由 USB-C 或 PoE 上游能力、主板自身负载、走线和连接器共同决定；USB-A VBUS 另经过 DIO7003 限流开关。
- **回复内容（解决方法）**：不要把 MP1658 的 3A 标注解释为 5V 端子可向外稳定输出 3A。Type-C 使用 5V/3A 电源时，应将其视为整板总输入预算，再扣除主板、屏幕、音频和其他外设峰值功耗。USB-A VBUS 按不超过 2A 连续负载设计；DIO7003 的限流点典型约 2.5A，规格范围约 2.1～3.1A。PoE 供电时应以实际配套 PoE 模块的 5V 输出额定值为准。4G 模块最好独立使用 5V/3A 或更高余量电源并与开发板共地；若共用一个电源，应从电源端分别给开发板和 4G 模块分支，不让蜂窝发射峰值电流经过板上 5V 针脚和 PCB 走线。
- **相关报错/日志**：⚠️ 官方未给出 5V 端子的对外持续输出电流额定值；PoE 模块的具体电流也需按实物料号确认。DIO7003 的 2A 连续值仅适用于 USB-A VBUS 通路。
