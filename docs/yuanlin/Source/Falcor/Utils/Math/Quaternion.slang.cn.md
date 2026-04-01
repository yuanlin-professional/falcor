# Quaternion.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/Quaternion.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

GPU 端的四元数运算工具函数。四元数以 `float4` 表示（xyz 为虚部，w 为实部）。支持四元数乘法、向量旋转、轴角旋转和方向间旋转计算。

## 函数
| 函数签名 | 说明 |
|----------|------|
| `float4 qmul(float4 q1, float4 q2)` | 四元数乘法（可微分） |
| `float3 rotate_vector(float3 v, float4 r)` | 用四元数旋转向量（可微分） |
| `float4 rotate_angle_axis(float angle, float3 axis)` | 从角度和轴构建旋转四元数 |
| `float4 from_to_rotation(float3 v1, float3 v2)` | 计算从 v1 到 v2 的旋转四元数 |

## 依赖关系
### import
- `Utils/Math/MathConstants.slangh`（通过 `#include`）

## 实现细节

- 四元数乘法和向量旋转标记为 `[BackwardDifferentiable]`，支持反向自动微分。
- 单位四元数定义为宏 `QUATERNION_IDENTITY = float4(0, 0, 0, 1)`。
- `from_to_rotation` 处理了平行和反平行向量的特殊情况。
