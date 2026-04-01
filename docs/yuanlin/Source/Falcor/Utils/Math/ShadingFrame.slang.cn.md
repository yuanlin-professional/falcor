# ShadingFrame.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/ShadingFrame.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

定义世界空间中的着色坐标系（TBN 正交基），用于材质着色计算中的本地/世界空间向量变换。支持可微分渲染。

## 结构体与接口

### `ShadingFrame` (实现 `IDifferentiable`)
| 字段 | 类型 | 说明 |
|------|------|------|
| `T` | `float3` | 着色切线方向（归一化） |
| `B` | `float3` | 着色副切线方向（归一化） |
| `N` | `float3` | 着色法线方向（归一化） |

## 函数
| 函数签名 | 说明 |
|----------|------|
| `__init(float3 normalW, float4 tangentW)` | 从法线和切线初始化（假设可安全正交化） |
| `static ShadingFrame createIdentity()` | 创建单位着色坐标系 |
| `static ShadingFrame createSafe(...)` | 安全创建（处理退化情况） |
| `float3 fromLocal(float3 v)` | 本地空间转世界空间（可微分） |
| `float3 toLocal(float3 v)` | 世界空间转本地空间（可微分） |
| `float getHandednessSign()` | 获取手偏性符号 (+1 右手/-1 左手) |
| `void orthonormalize(float4 tangentW)` | 正交化 TBN 基 |
| `bool orthonormalizeSafe(float4 tangentW)` | 安全正交化（处理零向量/平行向量等） |

## 依赖关系
### import
- `Utils.Math.MathHelpers`

## 实现细节

- 所有关键方法标记为 `[Differentiable]`，支持可微分渲染管线。
- `createSafe` 和 `orthonormalizeSafe` 处理了切线为零、切线与法线平行、含 NaN 等退化情况，在无法正交化时会自动生成一个切线空间。
