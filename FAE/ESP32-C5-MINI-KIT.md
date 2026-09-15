---
产品: ESP32-C5-MINI-KIT
tags: [FAE, ESP32-C5, WiFi6, BLE, 电池]
商品链接: https://www.waveshare.net/shop/ESP32-C5-MINI-KIT.htm
---

# ESP32-C5-MINI-KIT

### 与 ESP32-C5-Pico 有什么区别
- **客户问题/现象**：比较算力、无线、尺寸和扩展。
- **涉及产品/型号**：ESP32-C5-MINI-KIT、ESP32-C5-Pico。
- **根因**：两者性能和无线规格基本相同，MINI-KIT更紧凑，Pico接口更多。
- **回复内容（解决方法）**：追求小体积并焊到底板选MINI-KIT；需要Pico扩展板、TCA9554扩展IO和插拔式电池座选Pico。程序移植需修改GPIO定义。
- **相关报错/日志**：无。

### MINI-KIT 能否使用锂电池
- **客户问题/现象**：误以为只有Pico支持电池。
- **涉及产品/型号**：ESP32-C5-MINI-KIT。
- **根因**：MINI-KIT也有单节锂电池供电与ETA6098充电管理，只是使用BAT/GND焊盘而非插座。
- **回复内容（解决方法）**：单节3.7V锂电池正极接VBAT/BAT、负极接GND；注意极性，不要向BAT输入5V。Pico的差别只是GH1.25插座更方便。
- **相关报错/日志**：无。

### 能否收发 2.4G、5G、5.8G 和蓝牙信号
- **客户问题/现象**：想把板卡当无线收发模块。
- **涉及产品/型号**：ESP32-C5-MINI-KIT。
- **根因**：支持协议内的2.4/5GHz Wi-Fi、BLE及802.15.4，但不是通用射频接收器/SDR；双频不能同时工作。
- **回复内容（解决方法）**：可收发2.4GHz和5GHz Wi-Fi，5GHz覆盖部分常称5.8G的Wi-Fi信道；支持BLE，不支持经典蓝牙。只能处理对应Wi-Fi/BLE/Zigbee/Thread协议，不能直接解调5.8G图传、遥控或私有调制信号。
- **相关报错/日志**：无。

