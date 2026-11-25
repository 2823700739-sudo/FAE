---
产品: ESP32-S3-DualEye-Touch-LCD-1.28
tags: [FAE, ESP32-S3, RTC]
---

# ESP32-S3-DualEye-Touch-LCD-1.28

> [!important] ⚠️ 产品参数 / 规格优先
> 参数与兼容性按准确型号和硬件/固件版本确认；保留未实测及待核实标记。

## ⚠️ 产品参数 / 规格（优先查看）

### Y1 是 RTC 吗，完全断电后能否保持时间
- **客户问题/现象**：截图询问晶振/RTC用途。
- **涉及产品/型号**：ESP32-S3-DualEye-Touch-LCD-1.28，勿与无Touch型号混用。
- **根因**：Y1为40MHz系统基准晶振，不是独立RTC；该图未配置独立RTC芯片。
- **回复内容（解决方法）**：日期时间可程序计时并联网同步；完全断电仍走时需外接带备用电池的RTC，并核对可用接口/电平。
- **相关报错/日志**：🔍 2026-10-28图纸结论，未实板测试。[产品图纸](https://files.waveshare.com/wiki/ESP32-S3-DualEye-Touch-LCD-1.28/ESP32-S3-DualEye-Touch-LCD-1.28.pdf)。

### 小智源码在哪里，能否自定义表情或让双屏显示不同内容
- **客户问题/现象**：客户寻找小智固件源码并询问表情定制。
- **涉及产品/型号**：ESP32-S3-DualEye-Touch-LCD-1.28；本次答复提到版本2.2.6。
- **根因**：历史发布的源码/资源流程与后续更新是不同版本信息，双屏异显需要应用逻辑支持。
- **回复内容（解决方法）**：本次回复称源码已放GitHub，版本2.2.6；可制作图片/GIF并按适配工程资源流程配置、打包和烧录自定义表情，双屏显示不同内容需自行修改。原GitHub链接和包结构未附，暂不猜仓库地址、资源格式或烧录偏移；“即将更新”仅记录当时预告，不当作已有新版本发布。
  2026-11-04补充：本次读取《确认小智源码版本》补足[源码入口](https://github.com/waveshareteam/ESP32-S3-DualEye-Touch-LCD-1.28/tree/main/xiaozhi-esp32)：CMakeLists.txt声明PROJECT_VER=2.2.6，main/idf_component.yml要求ESP-IDF≥5.5.2；声明版本不证明与上游标签逐字一致。main/display/lcd_display.cc的LcdDisplay::SetEmotion包含静态/GIF路径，main/assets.cc负责资源装载；按既有情绪名映射后更新assets分区，具体打包仍跟随该版本。boards/waveshare/esp32-s3-dualeye-lcd-1.28/display_manager.cc目前给两屏同一表情指令，左右异显/眨眼需自行改显示和动画逻辑。源码核对未上板；不因内部目录名无Touch就换归非Touch产品。
- **相关报错/日志**：状态：已提供源码入口答复，未附定制/双屏异显实测；注意与非Touch DualEye板区分。来源：本聊天用户提供的27项客户支持汇总，2026-11-04入库；原个案时间及原始附件未提供。

### 能否接树莓派4B控制双眼，能否直接显示桌面
- **客户问题/现象**：客户问本板能否与Raspberry Pi4B配合使用。
- **涉及产品/型号**：ESP32-S3-DualEye-Touch-LCD-1.28、Raspberry Pi4B。
- **根因**：本产品为自带ESP32-S3的双屏开发板，显示由S3驱动，不是树莓派原生HDMI/DSI显示模块。
- **回复内容（解决方法）**：可设计USB串口、3.3V UART或Wi-Fi指令链路，树莓派发送表情/状态，S3解析后控制双屏，需两端程序及协议。不能像HDMI/DSI屏直接接Pi显示桌面；USB连接能否作为业务串口仍依赖固件枚举/协议，UART需核对针序电平和共地，不把连上线等同完成驱动或视频传输。
  2026-11-24增量补充：PC/树莓派下发表情指令可优先走原生USB Serial/JTAG，或3.3V UART：LCD1-Board SH1.0 14Pin中9脚RX=GPIO44、10脚TX=GPIO43、2/6脚GND，主机TX接9、RX接10。也可自定义BLE GATT（不是经典SPP）或2.4GHz Wi-Fi HTTP/WebSocket。04_SD_gif_show的BOOT切换GIF可作入口；EYE=happy等仅是自定义协议示意，出厂固件没有因此自动支持该命令。仍需实现通信解析、素材映射和显示线程同步。
- **相关报错/日志**：🔍 方案说明，无树莓派与本板联调、桌面显示或现成协议固件验证。来源：2026-11-09《确认双眼屏能否连接树莓派4B》，本次完整读取原问答；除另注外未实板复测。
  🔍 无主机联动实测；排针引脚按对应子板/版次核对。 来源：2026-11-20《查找表情控制通信方式》。
