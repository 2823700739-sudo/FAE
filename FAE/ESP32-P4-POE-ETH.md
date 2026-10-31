---
产品: ESP32-P4-POE-ETH
aliases: [ESP32-P4-ETH系列]
tags: [FAE, ESP32-P4, PoE, Ethernet]
商品链接: https://www.waveshare.net/shop/ESP32-P4-POE-ETH.htm
---

# ESP32-P4-POE-ETH

> [!important] ⚠️ 产品参数 / 规格优先
> 本页已将刷新率、供电/电流/电压、接口数量与类型、芯片/存储、尺寸结构及外设能力等**产品参数类问题集中置顶**。参数可能随硬件版本或批次变化；对外回复前请核对准确型号、PCB/芯片版本及条目中的 ⚠️/🔍 标记。

## ⚠️ 产品参数 / 规格（优先查看）

### 是否板载 C6 或 C5，能否直接使用无线协处理器通信
- **客户问题/现象**：确认该产品是否也有P4与C6/C5的板内通信通道。
- **涉及产品/型号**：ESP32-P4-POE-ETH。
- **根因**：本型号未板载ESP32-C6/C5；不能把其他P4-WIFI6或Module型号的SDIO无线架构直接套用。
- **回复内容（解决方法）**：P4本身无Wi-Fi/Bluetooth射频，因此本板没有可直接初始化的板载C6/C5 Hosted无线链路。PoE扩展的是供电，原板网络可使用以太网，不会因此增加无线芯片。若外接C6/C5，需自行设计供电、接口、复位和匹配的主从固件，现有其他板引脚/配置不能照搬；未确认具体外接方案。
- **相关报错/日志**：🔍 依据本地Docs_zh `ESP32-P4-ETH/Product-Overview.md`的型号范围和板载资源核对，未做外接适配。[官方资料](https://docs.waveshare.net/ESP32-P4-ETH/)。

### PoE 电压是多少，是否只走网线 1/2/3/6
- **客户问题/现象**：询问PoE输入/输出电压和Mode A/Mode B线对。
- **涉及产品/型号**：ESP32-P4-POE-ETH、ESP32-P4-ETH。
- **根因**：原理图确认PoE模块向主板输出 `VCC1_5V`，并把 RJ12、RJ36、RJ45、RJ78 都送入模块；具体模块输入范围未在该产品公开文档单独标明。
- **回复内容（解决方法）**：使用标准 IEEE 802.3af PoE交换机/注入器（常见标称48V），不要用未经确认的12V/24V被动PoE。百兆数据走1/2、3/6；Mode A可在数据线对幻象供电，Mode B用4/5、7/8空闲线对。板卡同时接入两组，实际模式由PSE决定，不由软件选择。
- **相关报错/日志**：🔍 历史所述37～57V、5V/2.5A来自同系列PoE模块资料；只有确认模块料号相同后才能直接套用。5V输出可由本板原理图确认。

### PoE 模块能输出多大功率
- **客户问题/现象**：询问配套PoE模块的最大输出功率，以及该功率能否全部提供给外设。
- **涉及产品/型号**：ESP32-P4-POE-ETH、ESP32-P4-NANO配套PoE模块。
- **根因**：微雪ESP32-P4-NANO官方Wiki列出的PoE模块规格为输入DC 37～57V、输出DC 5V/2.5A、IEEE 802.3af，因此标称最大输出功率为 `5V × 2.5A = 12.5W`。
- **回复内容（解决方法）**：PoE模块总输出上限为12.5W，该功率需要由ESP32-P4主板和所有屏幕、摄像头、USB及音频外设共同使用，并非额外可供外设使用的12.5W。设计时应预留启动峰值和温升余量；蜂窝5G模块峰值电流可能超过2.5A，建议使用独立电源，不建议与主板共用该PoE模块。
- **相关报错/日志**：🔍 ESP32-P4-ETH产品页未单独标明配套PoE模块的最大输出电流；若模块料号或硬件版本不同，应以模块丝印、规格书或售后确认结果为准。参考：https://www.waveshare.com/wiki/ESP32-P4-Nano-StartPage

## 其他问题

### 开发 SIP 终端可以参考哪些实例
- **客户问题/现象**：使用该板开发 SIP 终端，希望取得类似实例。
- **涉及产品/型号**：ESP32-P4-POE-ETH、ESP-ADF、SIP/RTP。
- **根因**：乐鑫 ESP-ADF 的 `adf_examples/protocols/sip_cli` 支持 P4 和 SIP 注册、呼叫、接听、挂断、双向音频及可选视频；其参考 P4 硬件是 Function EV Board，并非已验证的微雪本板移植工程。
- **回复内容（解决方法）**：先用微雪 Platform 仓库的 `11_ethernetbasic`、`12_I2SCodec` 跑通网口及录音/播放，再移植 `sip_cli`，将默认 Wi-Fi 网络路径适配为本板以太网，配置 Codec、I2C/I2S 和功放控制。初期只做 G.711A 纯语音，让板卡和电脑软电话注册同一 PBX，测试 `sip start`、`sip call 1002`、`sip answer`、`sip bye`；随后再加视频、AEC 和公网功能。ESP-IDF/ADF 版本按所选示例版本的 README 与依赖固定。[SIP CLI 官方说明](https://raw.githubusercontent.com/espressif/esp-adf/master/adf_examples/protocols/sip_cli/README.md)、[微雪基础例程](https://github.com/waveshareteam/ESP32-P4-Platform)。
- **相关报错/日志**：⚠️ 未找到本型号专用且经过实测的 SIP 工程，尚未编译/测试该移植组合。来源：2026-10-08 客户问答。
