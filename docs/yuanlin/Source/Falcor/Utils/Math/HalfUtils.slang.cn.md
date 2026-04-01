# HalfUtils.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/HalfUtils.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供 fp16（半精度浮点数）的方向舍入转换工具函数。在需要保证区间算术正确性的场景中使用，例如光线追踪中的保守包围盒计算。

## 函数
| 函数签名 | 说明 |
|----------|------|
| `uint f32tof16_roundDown(float value)` | fp32 转 fp16，向下取整到最近可表示数 |
| `uint f32tof16_roundUp(float value)` | fp32 转 fp16，向上取整到最近可表示数 |

## 依赖关系
### import
- 无外部依赖

## 实现细节

- 利用标准 `f32tof16` 转换后检查结果方向，若舍入方向不对则手动调整到相邻 fp16 值。
- 对于正零/负零的特殊情况进行了专门处理。
