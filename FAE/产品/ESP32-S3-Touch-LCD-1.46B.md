---
产品: ESP32-S3-Touch-LCD-1.46B
tags: [FAE, ESP32-S3, 录音, MicroSD]
商品链接: https://www.waveshare.net/shop/ESP32-S3-Touch-LCD-1.46B.htm
---

# ESP32-S3-Touch-LCD-1.46B

> [!important] ⚠️ 产品参数 / 规格优先
> 参数与功能按准确型号、硬件版本和固件使用；图纸/源码判断、编译与实机验证分别记录。

## ⚠️ 产品参数 / 规格（优先查看）

### 能否开发便携录音器，WAV 存卡需要补哪些功能
- **客户问题/现象**：客户希望把该板用于录音设备开发。
- **涉及产品/型号**：ESP32-S3-Touch-LCD-1.46B，Waveshare SKU29564，1.46系列资料。
- **根因**：板载麦克风、microSD和音频回放/触摸提供语音录音原型基础；官方测试示例列语音识别和音乐播放，不等于现成连续录音保存固件。
- **回复内容（解决方法）**：可按ESP-IDF官方01_ESP32-S3-Touch-LCD-1.46-Test复用麦克风采集、SD卡及播放代码，再增加WAV封装、开始/停止、文件列表、缓存和写卡错误处理。先用16kHz/16-bit/单声道PCM WAV验证，数据量约115.2MB/小时（约109.9MiB，另加文件头），这是格式计算而非存卡性能实测。远场会议、连续长录与电池续航需场景测试；B版按SKU和实际硬件确认，不套其他产品的音频引脚。示例SDK按其README/manifest匹配，当前Wiki要求IDF5.4.1及以上，不表示任意更新SDK已验证。
- **相关报错/日志**：🔍 来源问答：2026-11-25《评估 ESP32-S3-Touch-LCD-1.46B 录音能力》；原答描述I2S采集，具体麦克风料号/引脚及WAV保存实现本次未独立核对；无编译、烧录或硬件录音测试。
- **来源与资料**：[准确B型号商品页](https://www.waveshare.net/shop/ESP32-S3-Touch-LCD-1.46B.htm)；[官方系列页及SKU对应](https://docs.waveshare.net/ESP32-S3-Touch-LCD-1.46/)；[官方ESP-IDF测试示例说明](https://docs.waveshare.net/ESP32-S3-Touch-LCD-1.46/ESP-IDF/)。
