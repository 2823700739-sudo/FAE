---
产品: Pico-DALI2
tags: [FAE, DALI, RMT, 兼容性]
商品链接: https://www.waveshare.net/shop/Pico-DALI2.htm
---

# Pico-DALI2

### 乐鑫 DALI 驱动如何适配 S3、C6、P4、C5 Pico
- **客户问题/现象**：希望四块主控逐一控制DA4-M调光器，已有独立总线电源。
- **涉及产品/型号**：Pico-DALI2、ESP32-S3/C6/P4/C5-Pico、DALI RMT。
- **根因**：该板为物理接口、乐鑫组件用RMT实现协议。所附组件已声明S3/C6/P4，C5需补目标声明后测试，不等于已兼容。
- **回复内容（解决方法）**：统一ESP-IDF≥5.5环境，按GPIO选RX/TX：S3=14/17、C6=5/14、P4=17/14；C5建议3/7，GPIO7需从主板物理10脚飞到模块9脚，避开主板9脚的电池采样GPIO6。模块RX在物理5、TX在9、VSYS在39、GND在38，模块4脚初测留空。设invert_tx=true/invert_rx=false、mem_block_symbols=0，C5清单及测试工程补esp32c5。DA1/DA2接专用限流总线电源和灯具，模块USB5V不是DALI总线电源。[乐鑫说明](https://docs.espressif.com/projects/esp-iot-solution/en/latest/electrical_lighting_solution/dali.html)。
- **相关报错/日志**：⚠️ 上述为所查图纸/代码候选接线，四板均待编译和实测；物理脚号与GPIO编号须分清。

### 忘记 DA4-M 短地址时如何确认
- **客户问题/现象**：旧Arduino例程可用，但不记得灯具地址。
- **涉及产品/型号**：Pico-DALI2、DA4-M、DALI短地址0～63。
- **根因**：旧程序先扫描有回复的短地址，无设备时才commission；数组初始化为0不能证明设备地址就是0。
- **回复内容（解决方法）**：先单独运行scan short addresses，记录当前地址并填入TEST_ADDR。换主控沿用同一地址；扫描不到时先做广播调光区分TX/RX，不立即RESET或重新commission。只有独立测试总线上的无地址灯具才考虑地址分配。
- **相关报错/日志**：🔍 单台无地址设备初次分配可能得到0，但必须以扫描回复确认。

### 兼容性测试 PASS 应如何验收
- **客户问题/现象**：要求可填写测试步骤和表格，确认发送、回读及稳定性。
- **涉及产品/型号**：Pico-DALI2、S3/C6/P4/C5测试。
- **根因**：发送API返回ESP_OK只代表提交成功，不能证明灯具执行；reply=-1不是有效0xFF回复。
- **回复内容（解决方法）**：每次单独测试初始化、广播实际灯光、短地址查询及亮度回读；要求err=ESP_OK且DALI_RESULT_IS_VALID(reply)，再做100次有效查询、10次调光、3次重启复测。不要整组运行含RESET/重新编址的测试。参考[测试步骤](C:/Users/admin/Documents/ChatGPT/P4/output/dali-compatibility/兼容性测试步骤.md)及[记录表](C:/Users/admin/Documents/ChatGPT/P4/output/dali-compatibility/兼容性测试记录表.md)。
- **相关报错/日志**：⚠️ 文档仅经文本检查，原工程未因该问答自动完成移植，四板结果均待测。
