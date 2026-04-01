# MathConstants.slangh 着色器文档

> 路径: `Source/Falcor/Utils/Math/MathConstants.slangh`
> 类型: Slang 着色器头文件
> 模块: Utils/Math

## 功能概述

定义 GPU/CPU 共享的数学常量和数值极限宏。在 GPU 端通过宏定义提供 `<cmath>`、`<cstdint>`、`<cfloat>` 中的常量；在 CPU 端直接包含对应标准库头文件。

## 常量定义

### 数学常量
| 宏 | 值 | 说明 |
|----|----|------|
| `M_PI` | 3.14159... | 圆周率 |
| `M_2PI` | 6.28318... | 2 * pi |
| `M_4PI` | 12.5663... | 4 * pi |
| `M_1_PI` | 0.31830... | 1/pi |
| `M_1_2PI` | 0.15915... | 1/(2*pi) |
| `M_1_4PI` | 0.07957... | 1/(4*pi) |
| `M_SQRT2` | 1.41421... | sqrt(2) |
| `M_SQRT3` | 1.73205... | sqrt(3) |
| `M_E` | 2.71828... | 自然对数底 |

### 浮点极限
| 宏 | 说明 |
|----|------|
| `FLT_MAX` | float 最大值 |
| `FLT_MIN` | float 最小正规格化值 |
| `FLT_EPSILON` | float 机器精度 |
| `DBL_MAX` / `DBL_MIN` / `DBL_EPSILON` | double 对应极限 |

### 半精度浮点极限
| 宏 | 说明 |
|----|------|
| `HLF_MAX` | half 最大值 |
| `HLF_MIN` | half 最小正规格化值 |
| `HLF_EPSILON` | half 机器精度 |

## 依赖关系
### import
- `Utils/HostDeviceShared.slangh`（通过 `#include`）

## 实现细节

- 必须使用 `#include` 而非 `import` 引入，因为 Slang 模块的 `import` 不会导出宏定义。
- 所有常量均为预计算的高精度值，避免编译器在 fp32 精度下进行常量折叠导致的精度损失。
