---
产品: 7-DSI-TOUCH-A
tags: [FAE, DSI, ESP-IDF, 组件依赖]
---

# 7-DSI-TOUCH-A

### esp_lcd_ili9881c 组件重名导致 CMake 失败
- **客户问题/现象**：工程同时解析到Espressif和Waveshare的同名ILI9881C组件。
- **涉及产品/型号**：7-DSI-TOUCH-A、`esp_lcd_ili9881c`。
- **根因**：`espressif/esp_lcd_ili9881c`与`waveshare/esp_lcd_ili9881c`都提供同一组件名，ESP-IDF无法决定使用哪一个。
- **回复内容（解决方法）**：检查所有 `idf_component.yml` 和 `dependencies.lock`，只保留一个依赖。Waveshare屏项目通常保留 `waveshare/esp_lcd_ili9881c: ^2.0.0`；Espressif官方工程则相反。随后删除 `managed_components`、`dependencies.lock`和build，重新reconfigure/build。
- **相关报错/日志**：截图中的 `ESP-TE is currently supported...` 只是功能提示，不是本次CMake失败根因。

