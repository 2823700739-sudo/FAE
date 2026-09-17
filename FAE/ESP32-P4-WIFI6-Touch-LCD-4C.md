---
产品: ESP32-P4-WIFI6-Touch-LCD-4C
tags: [FAE, ESP32-P4, LCD, MIPI-DSI, 触摸]
商品链接: https://www.waveshare.net/shop/ESP32-P4-WIFI6-Touch-LCD-4C.htm
---

# ESP32-P4-WIFI6-Touch-LCD-4C

### LCD 和触摸使用什么控制器
- **客户问题/现象**：外国客户询问显示屏使用什么 controller，并追问 JD9365 信息来源。
- **涉及产品/型号**：ESP32-P4-WIFI6-Touch-LCD-4C。
- **根因**：官方商品页和 README 没有直接给出 LCD 模组 BOM/芯片丝印；JD9365 来自官方 BSP 对 `esp_lcd_jd9365` 的软件依赖。
- **回复内容（解决方法）**：对外严谨表述为：`According to the official BSP, the display is driven using the JD9365-compatible MIPI-DSI driver. The main processor is the ESP32-P4NRW32, and the touch controller is GT9271.` 即主控为 ESP32-P4NRW32，LCD 使用 JD9365 兼容的 2-lane MIPI-DSI 驱动，触摸控制器为 GT9271，并使用 GT911 兼容驱动。
- **相关报错/日志**：🔍 JD9365 可由 BSP 驱动依赖确认，但目前没有商品页 BOM 或实物丝印证据，不宜绝对表述为已确认的屏幕物料型号。
