---
产品: ESP32-P4-WIFI6-DB
tags: [FAE, ESP32-P4, 音频, I2C, 摄像头]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-DB.htm
---

# ESP32-P4-WIFI6-DB

> [!important] ⚠️ 产品参数 / 规格优先
> 对外答复前核对准确型号、硬件/固件版本及⚠️/🔍标记；资料与源码核对不等于实板测试。

## ⚠️ 产品参数 / 规格（优先查看）

### 与普通 WIFI6 的无线芯片差异及固件能否通用
- **客户问题/现象**：比较两板，以及出厂BIN、自研工程能否直接互刷。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB。
- **根因**：本次所查DB使用ESP32-C5，支持2.4/5GHz Wi-Fi；普通WIFI6使用ESP32-C6，Wi-Fi为2.4GHz。两者板级连接、从机固件及芯片修订配置仍需分别匹配。
- **回复内容（解决方法）**：出厂固件按准确产品、屏幕和芯片修订选择，不把相近接口/同为32MB Flash和32MB PSRAM当作BIN通用依据。仅GPIO等自研应用在实际引脚、目标修订和配置完全匹配时才可评估复用；无线工程需按C5/C6配置ESP-Hosted和匹配从机固件后重编译。参考[DB资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DB/)与[普通WIFI6资料](https://docs.waveshare.net/ESP32-P4-WIFI6/)，后续批次先核对实物料号。
- **相关报错/日志**：🔍 原问答结论归档，未新增实板验证。 来源：2026-11-23《对比两款 ESP32-P4-WIFI6 开发板》。

### 主控是三路还是双路 CAN，是否有现成 CAN 端子
- **客户问题/现象**：客户询问本板CAN路数，希望直接连接CAN总线。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB、ESP32-P4 TWAI。
- **根因**：P4包含3个经典CAN/TWAI控制器；所查DB板未板载CAN收发器或CANH/CANL端子，控制器数量不等于可直接接线的物理接口数量。
- **回复内容（解决方法）**：对客说明“主控支持3路经典CAN，使用时每路外接一个CAN收发器”。如需3路，从扩展口选择6个实际可用GPIO配成3组TX/RX，核对与已启用外设的占用、收发器逻辑电平、总线终端和接地。P4 TWAI不支持CAN FD；不要把GPIO直接接CANH/CANL。
- **相关报错/日志**：🔍 2026-11-24《确认 ESP32-P4-WIFI6-DB 的 CAN 数量》；文档/图纸判断，未三路并行测试。
- **来源与资料**：[微雪产品与引脚](https://docs.waveshare.net/ESP32-P4-WIFI6-DB/)；[乐鑫P4 TWAI说明](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32p4/api-reference/peripherals/twai.html)；[[FAE/资源/资料/ESP32-P4-WIFI6-DB/ESP32-P4-WIFI6-DB-原理图-2026-11-25.pdf|原问答参考原理图第1页]]。

## 其他问题

### 接摄像头后 ES8311 不应答，音频初始化 abort 重启
- **客户问题/现象**：音频日志ES8311 Open fail后循环重启；摄像头插着时GPIO7/8扫描仅0x0C/0x36，拔掉后ES8311可用。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB，07_i2s_codec、ES8311；相机型号未确认，不套DB-U/C5版参数。
- **根因**：当前直接故障是Codec I2C读写失败→初始化失败→主动abort；插拔对照缩小到共享I2C/供电影响，但电气与软件原因均未确诊。GPIO53重复初始化、Flash32MB而固件16MB等不是日志中直接触发错误。
- **回复内容（解决方法）**：按[本板资料](https://docs.waveshare.net/ESP32-P4-WIFI6-DB/)查共享SDA7/SCL8和ES8311七位0x18；本机Codec日志dev30为十六进制0x30（含R/W位），驱动内部右移，不能直接改成0x18。先插摄像头但只跑纯音频、不初始化相机：仍失败则断电检查排线/方向、相机模块、Codec供电及SDA/SCL空闲电平/上升沿；仅启相机后失败则查重复初始化/释放I2C和并发访问。原扫描已100kHz，0x0C/0x36不与0x18直接地址冲突，能扫其他器件不证明Codec通路正常。留底层NACK/超时码并用正常板对照。原vendor console中i2cdump -s16是参数错误，-s仅1/2/4，可用i2cdump -c 0x18 -s 1；不推广该私有命令到所有终端。
- **相关报错/日志**：⚠️ 合并2026-11-12《排查可能原因》与11-13《排查摄像头影响 ES8311》全部追问：I2C_If Fail to read/write dev30→ES8311 Open fail→speaker codec init failed→abort。客户只确认拔摄像头后可工作，无插相机纯音频复测/电压波形，未判芯片损坏或升级已修复。

### OV5647 绿色变粉红，传感器翻转与 Bayer 排列如何排查
- **客户问题/现象**：05_mipi_csi摄像头绿色显示为粉红，客户后续确认OV5647。
- **涉及产品/型号**：ESP32-P4-WIFI6-DB-main/examples/esp_idf/05_mipi_csi，OV5647、ISP/PPA；客户IDF版本未回。
- **根因**：⚠️ 优先怀疑原工程强制HFLIP/VFLIP改变RAW Bayer排列，但ISP仍用原配置；不是已确诊传感器损坏或简单RGB通道互换。乐鑫[Issue88](https://github.com/espressif/esp-video-components/issues/88#issuecomment-3014636300)说明传感器翻转需匹配Bayer模式。
- **回复内容（解决方法）**：先在main/app_video.c的app_video_open移除/注释V4L2_CID_VFLIP和V4L2_CID_HFLIP强制设置，以原始方向重新编译烧录看颜色；需要改方向时在RGB转换后的main/main.c PPA mirror_x/mirror_y处理。原聊天已改本地代码并补排障说明，本次仅读当前代码确认翻转被移除、PPA镜像参数在187行附近；未重新改源码或构建。若仍偏色，补实际传感器模式、Bayer配置、格式和IDF/组件版本再定位。
- **相关报错/日志**：⚠️ 来源：2026-11-18《排查摄像头画面颜色异常》3轮，源码检查完成，未编译/烧录/上板确认绿色恢复；本次不把修代码当已解决。
