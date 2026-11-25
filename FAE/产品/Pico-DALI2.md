---
产品: Pico-DALI2
tags: [FAE, DALI, RMT, 兼容性]
商品链接: https://www.waveshare.net/shop/Pico-DALI2.htm
---

# Pico-DALI2


## ⚠️ 产品参数 / 规格（优先查看）

### DALI 能否挂多个灯具和控制器，网关能否同时发送
- **客户问题/现象**：客户问同一DALI总线上能否接网关、Pico-DALI2与多个灯具。
- **涉及产品/型号**：Pico-DALI2、DALI-2子网、主控/网关程序。
- **根因**：多设备寻址不等于多个不协调发送的控制器可同时工作；总线电源和模块本地VSYS是不同供电。
- **回复内容（解决方法）**：按原答引用[DALI Alliance系统说明](https://www.dali-alliance.org/dali/systems.html)，DALI-2每子网最多64个灯具驱动装置+64个控制设备，可单播/组播/广播；多个控制器需各端支持多主机仲裁，不能由设备数推定任意网关/Pico程序可同时发。按[供电指南](https://www.dali-alliance.org/data/downloadables/3/4/8/dali-quick-start-guide_public-v1_1_april-2023.pdf)所有总线电源最大输出电流合计不超过250mA，并核对供电方案。故障定位时让网关停止发送但保留合规总线供电，Pico单独发；多设备本身不能解释异常低电平8.9V。
- **相关报错/日志**：🔍 来源：2026-11-12《整理 Pico-DALI2 产品 Wiki》中的总线追问；无客户网关多主机兼容或总线满载实测。

## 其他问题

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

### 总线实际低电平为 8.9V，反相后仍无改善怎么办
- **客户问题/现象**：客户总线高约16V、低约8.9V；后续明确已经反相，实际低电平仍异常。
- **涉及产品/型号**：Pico-DALI2，S8050、光耦T4、Q1 2N60G、扩展板VSYS；主控/网关型号未给。
- **根因**：⚠️ 先区分万用表通信平均值与示波器低平台；已反相仍失败则排查发送级未充分拉低。S8050驱动T4，实际下拉由Q1完成，不能直接判其中元件损坏。
- **回复内容（解决方法）**：原答按图分析TX高触发下拉、TX低释放，旧案例反相仅为初始排查；客户已做后转查VSYS→T4驱动和Q1。索要主控/程序、扩展板供电接线、VSYS对GND实测，以及DA1—DA2示波器波形与网关限流参数。网关内置合规DALI总线电源可以正常用，不因内置供电就判错。网关停发、保留供电、Pico单发作对照；正确VSYS/接线和配套示例下仍拉不到低电平，进一步查T4/Q1驱动、换板对比。原答引用[ST AN3298](https://www.st.com/resource/en/application_note/an3298-stm8s-dali-slave-library-stmicroelectronics.pdf)发送低上限4.5V、接收低上限6.5V，8.9V平台处在不确定区域；不把万用表平均8.9V直接套此判定。
- **相关报错/日志**：⚠️ 来源：2026-11-12《整理 Pico-DALI2 产品 Wiki》连续3轮，原图位于本地output/pico-dali2-review/Pico-DALI2.pdf；客户仅确认反相和低平台，未回VSYS/驱动测量，无元件确诊或修复成功。
