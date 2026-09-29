---
产品: ESP32-S3-Touch-LCD-3.49 V2
tags: [FAE, ESP32-S3, 电源, 开关电路]
商品链接: https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-3.49
---

# ESP32-S3-Touch-LCD-3.49 V2

### PWR 键、电池供电自保持与 USB 关机限制
- **客户问题/现象**：根据 V2/Rev1.1 原理图询问 Key1、Q1/Q2、T1、SYS_EN 的开关控制逻辑，以及为何 USB 供电下软件关机可能不起效。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-3.49 V2、Key1、Q1/Q2、T1。
- **根因**：按设计意图，仅电池供电时按 Key1 使 Q2 P-MOS 栅极拉低，`VBAT→VSYS` 上电；程序将 `SYS_EN`（EXIO6）拉高使 T1 维持 Q2 导通，拉低则应解除自保持。USB 的 `VBUS` 另经 D1 直接给 `VSYS` 供电，所以 Q2 关闭也不能切掉 USB 供电。⚠️ 所附图中 Q1 亦跨 `VBAT/VSYS`、栅极接 VBUS 且有下拉，按 AO3401 P-MOS 特性推断，拔 USB 后它可能形成绕过 Q2 的电池通路；这与官方“仅电池可用 PWR 键开关机”的描述不完全一致，需以实板版次和装配验证。
- **回复内容（解决方法）**：Key1 按下经 D4 启动、D3 使 `SYS_OUT` 供程序识别按键；Key2 是 BOOT、Key3 是 RESET。软件启动后将 `SYS_EN` 置高维持供电，需要关机时置低；插着 USB 时不能期望 Q2 切断整板。验证 Q1 疑点时拔掉 USB、松开 Key1、将 `SYS_EN` 置低，测 `VSYS/3V3` 是否下降；若仍有电，再核对实板 Q1 的装配、极性和走线，不要只凭 PDF 判定产品无法关机。[官方 Wiki](https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-3.49)、[版本资料](https://docs.waveshare.com/ESP32-S3-Touch-LCD-3.49/Resources-And-Documents)。
- **相关报错/日志**：🔍 Q1 绕过 Q2 属原理图推断，未经过客户实物测量；V2 丝印可能为 Rev1.1。
