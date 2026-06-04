# HAL_9_ADC

## 简介

本项目基于 STM32F103 系列 MCU 和 STM32 HAL 库，演示了使用 ADC 读取模拟电压，并将 ADC 值和对应电压通过 OLED 屏幕显示。
主程序使用 STM32CubeMX 生成的 CMake 构建配置，并结合自定义 OLED 显示模块输出测量结果。

## 主要功能

- 初始化 STM32F103 HAL 外设和系统时钟
- 配置 ADC1 读取通道 ADC1_IN6（PA6）模拟输入
- 启动 ADC 校准并进行单次转换
- 计算 12 位 ADC 采样值对应的电压（参考电压 3.3V）
- 通过软件 I2C 驱动 OLED 显示采集结果

## 注意事项

- 本项目默认使用 `HAL_ADC_PollForConversion()` 进行 ADC 轮询读取。
- 如果启用 ADC 模拟看门狗中断（`ADC_IT_AWD`），不要在看门狗回调中直接调用 `HAL_ADC_GetValue()`，否则会读取 `DR` 并清除 `EOC`，可能导致轮询等待失败。
- 对于看门狗中断，应选择中断方式 `HAL_ADC_Start_IT()` 或关闭 AWD 中断，避免轮询与中断逻辑冲突。

## 关键文件

- `CMakeLists.txt`：项目根 CMake 构建脚本
- `CMakePresets.json`：配置和构建预设，支持 `Debug` 和 `Release`
- `config.ioc`：STM32CubeMX 项目配置
- `Core/Src/main.c`：主程序入口，包含 ADC 读取和 OLED 显示逻辑
- `Core/Src/adc.c`：ADC1 初始化与通道配置
- `Core/Inc/adc.h`：ADC 模块接口声明
- `Core/Src/OLED.c`：OLED 控制逻辑与软件 I2C 实现
- `Core/Inc/OLED.h`：OLED 功能接口声明
- `Core/Inc/OLED_Font.h`：OLED 字库数据
- `cmake/user_sources.cmake`：自定义源文件和 include 路径注册
- `Drivers/`：STM32 HAL 库源文件和 CMSIS 头文件

## 构建环境与依赖

- CMake 3.22 及以上
- Ninja 构建器
- ARM GCC 交叉编译器（如 `arm-none-eabi-gcc`）
- STM32 HAL 库（包含在 `Drivers/` 目录中）

## 构建步骤

推荐使用 VS Code 的 CMake 工具或命令行：

```bash
cd d:/Electronics/HAL_Projects/HAL_9_ADC
cmake --preset Debug
cmake --build --preset Debug
```

## 运行与下载

1. 生成固件之后，使用 ST-Link 或其他支持的下载工具烧录生成的 ELF/HEX/BIN 文件到目标板。
2. 重新上电后，OLED 屏幕应显示 ADC 采样值和计算电压。

## 硬件说明

- 目标 MCU：STM32F103 系列
- ADC 输入通道：`ADC1_IN6`，引脚 `PA6`
- OLED 软件 I2C 默认引脚：
  - `PB8`：SCL
  - `PB9`：SDA

## 许可

MIT License
