# BSDFIntegrator.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFIntegrator.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/Materials

## 功能概述

BSDF 半球积分的 GPU 计算着色器实现。对各向同性 BSDF 在上半球进行蒙特卡洛积分，使用分层采样和线程组内归约。

## 结构体与接口

### `BSDFIntegrator`

- **职责**: 在 GPU 上执行 BSDF 积分和结果归约

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `gridSize` | `uint2` | 积分网格大小 |
| `gridCount` | `uint` | 积分网格数量（每个入射方向一个） |
| `resultCount` | `uint` | 每个网格的中间结果数 |
| `materialID` | `uint` | 场景中的材质 ID |
| `cosThetas` | `StructuredBuffer<float>` | 入射方向的 cos(theta) |
| `results` | `RWStructuredBuffer<float3>` | 中间结果缓冲区 |
| `finalResults` | `RWStructuredBuffer<float3>` | 最终结果缓冲区 |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `executeIntegration(uint2, uint, uint3)` | 在半球上积分 BSDF，每线程处理一个网格单元 |
| `reduceGroup(float3, uint, uint3)` | 线程组内归约并写入中间结果 |
| `executeFinal(uint, uint)` | 将中间结果归约为最终值 |

## 入口点

| 入口点 | 线程组大小 | 说明 |
|--------|-----------|------|
| `mainIntegration` | [32, 32, 1] | 积分主入口 |
| `mainFinal` | [256, 1, 1] | 最终归约入口 |

## 依赖关系

### import
- `Scene.Shading`
- `Utils.Math.MathHelpers`
- `Utils.Sampling.UniformSampleGenerator`

### include
- `Utils/Math/MathConstants.slangh`
