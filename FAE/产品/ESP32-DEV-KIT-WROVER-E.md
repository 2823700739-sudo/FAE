---
产品: ESP32-DEV-KIT-WROVER-E
tags: [FAE, ESP32, HFP, 音频]
商品链接: https://www.waveshare.net/shop/ESP32-DEV-KIT-WROVER-E.htm
---

# ESP32-DEV-KIT-WROVER-E

### HFP 免提通话音质差是否为 Wi-Fi 同频影响
- **客户问题/现象**：HFP连接手机通话时声音差，询问同频影响及外接无线模块。
- **涉及产品/型号**：ESP32-DEV-KIT-WROVER-E、经典蓝牙HFP、Wi-Fi共存。
- **根因**：ESP32 Wi-Fi/蓝牙共享2.4GHz射频并分时；断续可能受共存影响，发闷也可能是CVSD窄带，杂音/回声另需查音频链路。
- **回复内容（解决方法）**：先完全停用板载Wi-Fi做同条件通话对照；改善则查扫描、重连、流量与共存。不改善则核对CVSD 8kHz/mSBC 16kHz协商、增益、位宽、缓冲和回声。分拆无线可作为开发方向，但用户最终确认的对外回复为“目前暂无推荐的外接蓝牙或Wi-Fi模块”，不沿用此前候选列表作官方推荐。[共存说明](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/coexist.html)。
- **相关报错/日志**：⚠️ 根因未确诊；先确认Wi-Fi究竟在板上还是手机运行。
