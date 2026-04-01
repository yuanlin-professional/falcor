# ToneMapperParams.slang 源码文档

> 路径: `Source/RenderPasses/ToneMapper/ToneMapperParams.slang`
> 类型: Slang 着色器模块（主机/设备共享）
> 模块: RenderPasses/ToneMapper

## 功能概述

该 Slang 模块定义了色调映射器在主机端（C++）和设备端（GPU 着色器）之间共享的参数结构体和枚举类型。通过 `HostDeviceShared.slangh` 头文件确保跨平台数据布局一致性。

## 结构体与接口

### `ToneMapperOperator`（枚举）

| 值 | 名称 | 说明 |
|----|------|------|
| 0 | `Linear` | 线性映射 |
| 1 | `Reinhard` | Reinhard 算子 |
| 2 | `ReinhardModified` | 带最大白色亮度的 Reinhard 算子 |
| 3 | `HejiHableAlu` | John Hable 的 ALU 近似 Jim Heji 胶片算子 |
| 4 | `HableUc2` | John Hable 的 Uncharted 2 胶片色调映射 |
| 5 | `Aces` | ACES 胶片色调映射 |

### `ToneMapperParams`（结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `whiteScale` | `float` | HableUc2 算子的白色缩放参数 |
| `whiteMaxLuminance` | `float` | ReinhardModified 算子的最大白色亮度 |
| `_pad0` | `float` | 填充（HLSL 打包规则） |
| `_pad1` | `float` | 填充 |
| `colorTransform` | `float3x4` | 颜色变换矩阵（包含白平衡和曝光） |

## 函数

无函数定义。

## 依赖关系/import

| 模块 | 说明 |
|------|------|
| `Utils/HostDeviceShared.slangh` | 主机/设备共享宏定义 |

## 实现细节

1. **数据布局**: 结构体严格遵循 HLSL 打包规则。使用 `float` 填充对齐，避免使用 `bool`（Visual Studio 中为 1 字节，HLSL 中为 4 字节）。

2. **颜色变换矩阵**: 使用 `float3x4` 而非 `float3x3`，以满足 HLSL 的 `cbuffer` 打包对齐要求。在 C++ 端通过 `float3x4(mColorTransform)` 从 `float3x3` 转换。

3. **命名空间**: 使用 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 宏包裹，在 C++ 中映射为 `namespace Falcor`，在 Slang 中无效。
