---
产品: ESP32-S3-Zero
tags: [FAE, ESP32-S3, UART, USB]
---

# ESP32-S3-Zero

> [!important] ⚠️ 产品参数 / 规格优先
> 对外答复前核对准确型号、硬件/固件版本及⚠️/🔍标记；资料与源码核对不等于实板测试。

## ⚠️ 产品参数 / 规格（优先查看）

### USB 虚拟 COM、UART TTL 和 RS232 有何区别
- **客户问题/现象**：客户追问虚拟串口号、232调试，需解释板上用了哪些串口。
- **涉及产品/型号**：ESP32-S3-Zero，UART0 GPIO43/44、原生USB GPIO19/20。
- **根因**：原生USB枚举COM是电脑侧接口，GPIO UART是3.3V TTL，RS232是另一电平标准；本板没有独立USB转TTL芯片。
- **回复内容（解决方法）**：按[所给原理图](https://www.waveshare.net/w/upload/f/fc/ESP32-S3-Zero-Sch.pdf)，UART0 TX=GPIO43经R6 499Ω到P15-1、RX=GPIO44到P15-2；Type-C D−/D+=GPIO19/20经22Ω。Type-C不占43/44，可在驱动/固件正确配置下提供USB虚拟串口供串口助手使用；外接TTL时3.3V、TX/RX交叉共地并检查日志占用。实际RS232设备需电平转换，不能直接接GPIO；先确认客户说的是串口软件还是RS232硬件。
- **相关报错/日志**：🔍 来源：2026-11-14《识别 ESP32-S3-Zero 串口》两轮；图纸连接已在原答核对，无本次Windows枚举或TTL收发测试。

## 其他问题
