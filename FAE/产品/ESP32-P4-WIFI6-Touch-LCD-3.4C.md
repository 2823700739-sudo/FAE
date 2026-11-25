---
产品: ESP32-P4-WIFI6-Touch-LCD-3.4C
tags: [FAE, ESP32-P4, LCD, ADC, NTC]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-3.4C.htm
---

# ESP32-P4-WIFI6-Touch-LCD-3.4C

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### P4 与板载 C6 如何连接和通信，是否需要另接串口
- **客户问题/现象**：询问本产品P4和无线芯片之间的连接、通信方式，以及是否需要外接串口/AT固件。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-3.4C、ESP32-C6、ESP-Hosted。
- **根因**：P4本身不带Wi-Fi/Bluetooth射频；本产品的板载ESP32-C6作为无线协处理器，通过SDIO连接P4，软件使用ESP-Hosted；SDIO物理总线与其上的RPC/网络数据不是同一概念。
- **回复内容（解决方法）**：正常架构为 `P4 应用 → esp_wifi_remote / ESP-Hosted Host ⇄ 板内 SDIO ⇄ ESP32-C6 ESP-Hosted Slave → 无线网络`。SDIO 的 CLK、CMD、DAT 信号已由板内走线连接，使用对应官方固件不需要再用杜邦线把 P4 UART 与 C6 UART 相连。P4 编写业务并经适配层使用 `esp_wifi_init()`、`esp_wifi_set_config()`、`esp_wifi_start()`、`esp_wifi_connect()` 等 API；C6 负责无线射频和从机业务。若启用 BLE，P4 的 Host 与 C6 Controller 还需匹配的 Hosted HCI/VHCI 支持，不能由 Wi-Fi 正常推断 BLE 已开启。若板上引出 C6 UART，通常用于独立烧录/日志维护，并非该产品默认的 Wi-Fi AT 主通道。保持本型号 P4 端依赖、SDIO 配置与 C6 从机固件配套，主从版本号数值不必相同；不要改刷普通 ESP-AT，也不要把 P4 镜像写给 C6。已有兼容从机固件时，日常只更新 P4 应用；仅烧 P4 不会自动更新从机。 支持2.4 GHz Wi-Fi 6与BLE，不把Wi-Fi 6等同5 GHz，也不把BLE等同经典蓝牙音频。
- **相关报错/日志**：🔍 本次为产品资料/软件架构核对，未逐板实测；SDIO具体引脚及下载接口按本型号实板/原理图，不套用其他P4板。[本型号官方资料](https://docs.waveshare.net/ESP32-P4-WIFI6-Touch-LCD-XC/)，本次板内连接依据本地 Docs_zh 的 `ESP32-P4-WIFI6-Touch-LCD-XC/Product-Overview.md`。核对日期：2026-10-31。

### 两板能否 Wi-Fi 发送与播放视频
- **客户问题/现象**：口述XC但链接3.4C，问服务器发视频客户端播放。
- **涉及产品/型号**：按提供链接为ESP32-P4-WIFI6-Touch-LCD-3.4C；XC具体版本仍应另确认。
- **根因**：网络收发可开发，编码不等于解码，文件传到不等于能播放。
- **回复内容（解决方法）**：同路由器或一AP一STA均可，服务器角色不等于热点。先MJPEG/HTTP逐帧，P4硬解JPEG，320×240或640×480/15fps仅PoC起点；H264需软件解码，MP4还需解封装，音频需另加传输同步。确认TF文件或实时相机、目标分辨率帧率，不能承诺性能。
- **相关报错/日志**：⚠️ 2026-10-30资料方案，未双板运行实测。

### 背面 40Pin 能接多少路 NTC，能否分别显示温度
- **客户问题/现象**：询问背面 40Pin 最多支持多少个 NTC 热敏电阻，以及各路温度能否分别显示。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-3.4C，硬件 rev1.1。
- **根因**：40Pin 表示接口触点数量，不是 40 路模拟输入。按当前公开原理图，接口引出 7 个 ADC 输入：GPIO20/21/22 对应 ADC1_CH4/5/6，GPIO49/50/51/52 对应 ADC2_CH0/1/2/3。
- **回复内容（解决方法）**：最多可直接接 7 路独立 NTC，并在程序中轮询 7 个 ADC 通道、分别换算后同时显示温度 1～7；这属于依次采样，不是严格同步采样。每个 NTC 都要配独立分压电阻，使用 3.3V 分压，不能向 GPIO 输入 5V。建议做 ADC 校准和平均滤波，并按 NTC 的 B 值或 Steinhart–Hart 参数换算。超过 7 路时可通过 I2C 多通道 ADC 或模拟多路复用器扩展。
- **相关报错/日志**：🔍 7 路结论基于当前公开 rev1.1 原理图；量产前应再次核对实际板卡版本。

## 其他问题

### emote 配网离线加载与首次刷屏死锁如何区分
- **客户问题/现象**：问初始化、挂载、播放顺序及无网络配网画面。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-3.4C、esp_emote_expression1.0.2/gfx3.0.5。
- **根因**：渲染任务过早刷第一帧并等待缺失完成通知可卡图形锁；挂载本地资源不要求联网。
- **回复内容（解决方法）**：先准备LCD/DSI/DPI提交与完成回调，再emote_init→mount_assets→load_assets（可合并mount_and_load）→set_anim_emoji。资源分区/格式/动画名须有效；初始化和资源卸载串行，ISR/完成回调不加载/阻塞。复制小缓冲到framebuffer模式用DPI on_color_trans_done通知emote_notify_flush_finished，不照搬SPI panel-IO回调；直接帧缓冲切换按对应释放事件同步。小智离线Apply应在Board构造/显示/音频就绪后、配网前；别整体提前含下载的CheckAssetsVersion，避免Board单例重入。
- **相关报错/日志**：⚠️ 2026-10-30源码结论，客户具体实现未明确，未实板复现或验证修复。
