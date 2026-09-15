---
产品: ESP32-P4-WIFI6-Touch-LCD-10.1
tags: [FAE, ESP32-P4, OTA, ESP32-C6, 固件, 视频]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-10.1.htm
---

# ESP32-P4-WIFI6-Touch-LCD-10.1

### 能否实现配网与 OTA
- **客户问题/现象**：希望设备首次启动配网，后续在线升级。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：硬件有32MB Flash和C6 Wi-Fi，具备条件；P4主程序OTA与C6协处理器升级是两套流程。
- **回复内容（解决方法）**：推荐第一版采用 SoftAP网页配网 + P4主程序 HTTPS OTA：凭据存NVS，配置 `ota_0/ota_1`双分区，升级后自检并支持回滚。暂不优先 BLE配网，因为 P4+C6 Hosted 架构兼容性更复杂。C6固件升级另行处理。
- **相关报错/日志**：无。

### C6 出厂 ESP-Hosted 版本、DIO/QIO 和恢复固件能否确认
- **客户问题/现象**：询问 C6 出厂版本、Bootloader/App Flash mode、能否提供恢复包。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：历史查询时微雪公开仓库未给出批次对应的 C6 完整恢复包和精确版本，P4端组件版本不能替代C6版本。
- **回复内容（解决方法）**：🔍 先通过 P4 API 或 C6 UART查询实机；拿到固件后分别用 `esptool.py --chip esp32c6 image_info`检查 bootloader与app。向厂家索取 bootloader、partition table、ota data、network_adapter、flash args、SHA-256及安全启动/加密状态。不能把第三方或最新上游镜像当作出厂恢复固件。
- **相关报错/日志**：无。

### 不接 J13 能否更新 C6
- **客户问题/现象**：希望通过 P4/SDIO 更新板载 C6。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：ESP-Hosted 支持 Slave OTA，但依赖 C6 正常启动并支持对应 RPC。
- **回复内容（解决方法）**：可以用 `esp_hosted_slave_ota_begin/write/end/activate` 从 HTTPS、P4 Flash、文件系统或SD卡更新 C6应用分区；这不是救砖通道。C6 bootloader/分区表损坏、旧固件不支持OTA或SDIO无法建立时，仍需 J13 的 IO9/U0RXD/U0TXD。
- **相关报错/日志**：无。

### 如何查询 C6 固件版本
- **客户问题/现象**：希望从 P4 端读取板载 C6 版本。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：需等 ESP-Hosted 连接完成后调用查询API；旧 C6固件可能不实现该RPC。
- **回复内容（解决方法）**：先查 C6自身启动日志中的 `ESP-Hosted-MCU Slave FW version :: X.Y.Z`。P4端可在连接从机后调用 `esp_hosted_get_coprocessor_fwversion()`。若超时，不能把 `0.0.0` 当真实版本，只能查 C6 UART日志或回读 Flash。
- **相关报错/日志**：`Req_GetCoprocessorFwVersion (0x15e)`。

### 查询结果显示 Co-proc 0.0.0 并 RPC 超时
- **客户问题/现象**：SDIO已启动，但读取 C6版本返回失败。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：`0.0.0` 是未上报版本时的占位值；旧 network_adapter固件没有实现新版版本查询RPC，或主从协议不匹配。
- **回复内容（解决方法）**：通信出现 `TRANSPORT_TX_ACTIVE`、`Coprocessor Boot-up` 说明基础SDIO可用；删除版本查询可避免每次约1秒超时。换成 Host端 1.0.2 也无效，因为它仍发送相同的 `0x15e`。要确认版本，应接 J13读 C6日志或回读 Flash；要使API可用则升级为配套固件。
- **相关报错/日志**：`Version mismatch: Host [2.12.0] > Co-proc [0.0.0]`、`Response not received for [0x15e]`。

### 提供的 ESP32-C6_006.bin 是什么版本
- **客户问题/现象**：希望从 C6 完整BIN中解析固件信息。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1、ESP32-C6_006.bin。
- **根因**：二进制镜像头与 app description 可提取版本，但不能证明板上已烧录同一文件。
- **回复内容（解决方法）**：历史解析结果：项目 `network_adapter`，应用版本 `release/ng-v1.0.2-330-g83efce6`，ESP-IDF `v5.4-dev-3602-ga97a7b0962`，编译时间 2024-10-18，DIO/80MHz/4MB。只有确认板载C6确实烧过该BIN，才能把它作为当前版本。
- **相关报错/日志**：无。

### 出厂 FactoryOnly 固件应选择哪种 Flash Mode
- **客户问题/现象**：询问 `ESP32-P4-WIFI6-Touch-LCD-10.1-FactoryOnly-260820.bin` 使用 DIO 还是其他模式。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：历史任务直接解析了 Bootloader 与 Factory App 镜像头。
- **回复内容（解决方法）**：该特定文件的 Bootloader（0x2000）和 Factory App（0x200000）模式字节均为 `0x02`，烧录工具 Flash Mode 选择 DIO。文件换版本后应重新解析，不能泛化到所有固件。
- **相关报错/日志**：模式字节 `0x02 → DIO`。

### 能否实现“U盘/TF卡插入即播任意 MP4”
- **客户问题/现象**：希望播放用户随带的 Main/High Profile、720p/1080p MP4，达到车载播放器体验。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-10.1。
- **根因**：ESP32-P4内置 H.264硬件编码器而非解码器；软件解码只支持受限Profile，720p性能不足。
- **回复内容（解决方法）**：只能支持预先转码、规格受控的 MP4，不能保证“任意格式”。可限定 H.264 Constrained Baseline、较低分辨率和受支持音频，启用双任务/IRAM优化并用PPA缩放。若必须兼容任意720p/1080p Main/High，应改用带 H.264/H.265 VPU、Linux/Android和成熟播放器框架的应用处理器；这属于第三方主控选型和完整播放器开发，不在开发板常规技术支持范围。
- **相关报错/日志**：历史参考性能：1280×720约10fps，640×480约31fps。

