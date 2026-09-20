---
产品: ESP32-P4-Module-DEV-KIT
aliases: [ESP32-P4-Module-DEV-KIT-A]
tags: [FAE, ESP32-P4, 摄像头, ESP32-C6, USB, UART]
商品链接: https://www.waveshare.net/shop/ESP32-P4-Module-DEV-KIT.htm
---

# ESP32-P4-Module-DEV-KIT

### 是否支持 IMX708 自动对焦摄像头
- **客户问题/现象**：原配 OV5647 不满足自动对焦需求，询问第三方 IMX708 驱动能否使用。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT、IMX708、DW9807。
- **根因**：板卡具备 2-lane MIPI-CSI/SCCB 硬件条件，但乐鑫和微雪没有官方 IMX708 量产适配；第三方组件只在相近 P4 板与 ESP-IDF 5.4.0 上实测。
- **回复内容（解决方法）**：⚠️ 可作为样机验证，不能承诺官方支持。可评估 `mushbraindave/esp_cam_sensor_imx`，核对 SDA=GPIO7、SCL=GPIO8、2 lanes，并启用 IMX708、DW9807、ISP/AF 相关配置。首次扫描应看到 IMX708 `0x1A` 和 VCM `0x0C`。实用输出以 1080p RAW10 约 28 fps 为主，第三方方案不使用 IMX708 原生 PDAF，量产前需验证 AF、稳定性、温升和 ISP 调校。
- **相关报错/日志**：正常识别示例：`detected IMX708, PID=0x0708`。

### v1.3 芯片运行异常是否可判定为 APM-560
- **客户问题/现象**：客户报告使能中断后静默停摆，认为 ESP32-P4 v1.3 存在 APM-560 硬件缺陷。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT，ESP32-P4 rev v1.3。
- **根因**：现有报告混入 Tick ISR/ROM 字符探针，独立 5V 供电也未验证，尚不足以证明 APM-560；APM-560需同时证明多 AHB Master、未授权访问与 APM 拦截。
- **回复内容（解决方法）**：使用全新未改动 ESP-IDF v5.5.5、官方 `00_board_check`/`02_HelloWorld`、正确 pre-v3 配置，并新建 build/sdkconfig；采用独立稳定 5V/2A，连续运行 30 分钟并做 20 次冷启动。官方原始例程仍稳定复现时再按板卡异常处理；如确认为 APM-560，应换 ESP32-P4 rev v3.1 或更高，不能笼统写“v3.x”。
- **相关报错/日志**：`CONFIG_ESP32P4_SELECTS_REV_LESS_V3=y`、`CONFIG_ESP32P4_REV_MIN_100=y`。

### P4 能否单独复位或断电重启 C6
- **客户问题/现象**：C6 热点长时间运行后消失，希望只恢复 C6，不重启整板。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT。
- **根因**：P4 与 C6 共用 `ESP_3V3`，没有软件控制的独立 C6 电源开关；P4 GPIO54 连接 C6 `CHIP_PU/EN`。
- **回复内容（解决方法）**：可将 GPIO54 拉低约 100 ms 后拉高来硬复位 C6；使用 ESP-Hosted 时还需停止 Wi-Fi/SDIO、复位后重新初始化 ESP-Hosted 与 Wi-Fi Remote。若要真正断电重启 C6，需要改硬件增加负载开关/MOS。建议同时抓 C6 UART 日志并加入通信心跳。
- **相关报错/日志**：官方配置常见 `Slave_Reset[54]`。

### P4 与 C6 如何通信，C6 应烧 AT 固件吗
- **客户问题/现象**：询问双芯片通信方式和 C6 固件类型。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT。
- **根因**：P4 是 Host，C6 是无线协处理器，主通道为 SDIO。
- **回复内容（解决方法）**：正常架构为 `P4 esp_hosted + esp_wifi_remote ⇄ SDIO ⇄ C6 ESP-Hosted Slave/network_adapter`。C6 出厂通常已烧好，不应烧普通 ESP-AT；否则 P4 端 Hosted API 无法通信。只有恢复或升级时才通过 C6 UART 烧录与 P4 端版本匹配的 Slave 固件。
- **相关报错/日志**：无。

### CH334F 连接 P4 的哪些引脚
- **客户问题/现象**：询问 CH334F 是否接 GPIO26/27，或接模组 48/49 脚。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT。
- **根因**：混淆了模组焊盘号、裸芯片封装脚和 GPIO 编号。
- **回复内容（解决方法）**：CH334F 上行口通过 FSUSB42 连接 P4 专用 USB 2.0 HS DP/DM。微雪模组焊盘为 48/49；裸芯片对应 USB_DM/DP 封装脚 49/50；它们都不是 GPIO48/49。GPIO26/27 属于另一组 USB FS OTG，不是板载 CH334F 通路。
- **相关报错/日志**：连接关系：`P4 USB_DP/DM → FSUSB42 → CH334F DPU/DMU`。

### 经 CH334F 的 USB 口无法识别设备
- **客户问题/现象**：设备接 P4 直出 USB 正常，接 HUB 扩展口失败。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT-A。
- **根因**：可能未启用 HUB 支持，或外设为 FS/LS，当前 P4 HS Host 经 HUB 不支持 TT。
- **回复内容（解决方法）**：断电后确认跳线切到 HUB 通路，启用 `CONFIG_USB_HOST_HUBS_SUPPORTED=y`，查看枚举日志；若是 TT 限制只能暂用直连接口。只有连 CH334 本身都不枚举时，才继续查跳线、供电和硬件。
- **相关报错/日志**：`sdio` 无关；应提供 USB 枚举日志和设备速度。

### 板上占用了哪些 UART，还能否增加串口
- **客户问题/现象**：认为板卡只有一个串口，询问当前占用情况。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT。
- **根因**：P4 UART0 固定用于 GPIO37/38 的 Type-C 烧录与日志；C6 UART 是另一颗芯片的独立串口。
- **回复内容（解决方法）**：建议保留 P4 UART0，UART1～UART4 可通过 GPIO Matrix 映射到 40Pin 的空闲 GPIO。配置时避开板载外设，引脚使用 3.3V TTL、TX/RX 交叉并共地。P4-C6 的 SDIO 不占用 P4 UART。
- **相关报错/日志**：无。

### P6 和 H5 的 5V 引脚能否作为输入或输出
- **客户问题/现象**：客户从排针给板卡供电失败，进一步确认 P6 的 1、3 号脚以及独立 H5 供电口的定义和方向。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT。
- **根因**：P6-1 与 H5-2 都连接公共 `VCC_5V` 电源轨，没有固定输入/输出方向；P6-3 和 H5-1 为 GND。P6-2 是 `ESP_3V3`，不能输入 5V。
- **回复内容（解决方法）**：P6 正确接法为 1脚接 5V、3脚接 GND；H5 为 2脚接 5V、1脚接 GND。板子由 USB/PoE 供电时，VCC_5V 对外约为 5V；也可从 P6-1 或 H5-2 输入稳定 5V 给开发板供电。外部供电会绕过 Type-C 前端 Q2/Q5 的部分保护，必须确保极性和电压正确，不建议同时连接外部 5V 与带 VBUS 的 USB。P6-1 的可用输出电流取决于上游电源和整板负载，不能把 U4 标注的 3A 最大能力理解为排针可对外输出 3A。若 VCC_5V 正常而 ESP_3V3 缺失，重点检查 U4；若接入后 5V 明显下降，检查电源限流、接触和板上短路。
- **相关报错/日志**：将 5V 接入 P6-2 会向 3.3V 电源轨灌入 5V，可能造成器件损坏，应立即断电。

### SMF5.0CA 的作用及短路判断
- **客户问题/现象**：询问电源原理图中 SMF5.0CA 的作用，以及供电起不来时如何判断该器件是否损坏。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT、SMF5.0CA。
- **根因**：SMF5.0CA 是跨接在 `VCC_5V` 与 GND 之间的双向 TVS，用于吸收静电、插拔尖峰和短时浪涌；它不是稳压器，也不负责接口隔离或供电方向控制。
- **回复内容（解决方法）**：正常 5V 下 TVS 基本不导通，瞬态过压时快速泄放到 GND。若受到浪涌后击穿短路，会把 5V 拉低并触发电源限流。断电后测量 `VCC_5V—GND`，若持续接近 0Ω，可抬起 TVS 一端复测，以区分 TVS 短路和后级短路。万用表二极管挡双向均显示不导通可能是正常现象；击穿和钳位电压应以实际料号规格书为准。
- **相关报错/日志**：⚠️ 仅凭 5V 起不来不能直接判定 TVS 损坏，需隔离器件后复测。

### 能否同时连接声音、DHT11 和电容指纹传感器
- **客户问题/现象**：希望同时连接 Sound Sensor、DHT11 Temperature-Humidity Sensor 和 Capacitive Fingerprint Reader。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT、Sound Sensor、DHT11、Capacitive Fingerprint Reader。
- **根因**：三类模块分别使用 ADC/GPIO、单总线 GPIO 和 UART，开发板的 GPIO 与外设控制器资源足够；但目前没有针对这三款模块的板级即插即用示例。
- **回复内容（解决方法）**：三款模块均建议使用 3.3V 供电并与开发板共地。Sound Sensor 的 AOUT 可接 GPIO20（ADC1_CH4）读取相对声音强弱，DOUT 可选接空闲 GPIO；它不能直接测得准确分贝值。DHT11 数据脚可接 GPIO4，读取间隔建议不少于约 1 秒，并确认数据线上拉。电容指纹模块使用 UART，TX/RX 交叉连接，应映射到 UART1～UART4 的空闲 40Pin GPIO，保留 GPIO37/38 的 UART0 给 CH343P 烧录和日志，避免与板载调试串口冲突；原版指纹模块默认波特率常见为 19200，带 `(B)` 的新版可能为 115200，应按具体型号手册确认。软件上分别使用 ESP-IDF ADC/GPIO、DHT 单总线驱动和 UART 驱动。
- **相关报错/日志**：🔍 具体 DOUT、DHT11 和指纹 UART 引脚应结合当前硬件版本的 40Pin 占用再次确认；外设若输出 5V 电平，必须增加电平转换。

### 与 ESP32-P4-NANO 相比哪款功能更多、性能更好
- **客户问题/现象**：客户在 ESP32-P4-Module-DEV-KIT 与 ESP32-P4-NANO 之间选型，询问功能和性能差异。
- **涉及产品/型号**：ESP32-P4-Module-DEV-KIT、ESP32-P4-NANO。
- **根因**：两款采用相近的 ESP32-P4、32MB PSRAM、16MB Flash 与 ESP32-C6 无线方案，核心计算和图像处理性能基本相同；主要差别在板级接口数量、体积和扩展便利性。
- **回复内容（解决方法）**：需要更多 USB 和扩展接口时优先 Module-DEV-KIT：提供 4 个 Type-A（1 个直连、3 个经 CH334 HUB）、40Pin、独立 I2C/I3C、C6 UART 与 5V 接口，适合多外设和综合开发。需要更紧凑、成本更敏感且单个 USB 已足够时选择 NANO。两者的百兆网、PoE 扩展、TF、MIPI-DSI/CSI 和无线能力相近，不能把接口更多表述为主控性能更强。
- **相关报错/日志**：无。
