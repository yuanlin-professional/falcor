# RGLAcquisition.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/RGLAcquisition.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/Materials

## 功能概述

RGL BRDF 虚拟测量的 GPU 着色器实现。包含回溯反射测量、核矩阵构建、幂迭代、Sigma 积分、theta 计算、VNDF 计算和 BRDF 采集等多个计算入口点。

## 结构体与接口

### `RGLAcquisition`

- **职责**: 在 GPU 上执行 BRDF 虚拟测量的各个阶段

## 入口点

| 入口点 | 线程组大小 | 说明 |
|--------|-----------|------|
| `measureRetroreflection` | [16,16,1] | 测量回溯反射 |
| `buildPowerIterationKernel` | [16,16,1] | 构建 Fredholm 核矩阵 |
| `powerIteration` | [16,16,1] | 幂迭代提取 NDF |
| `integrateSigma` | [16,16,1] | 数值积分 Sigma |
| `sumSigma` | [16,16,1] | 汇总 Sigma |
| `computeTheta` | [16,16,1] | 计算测量角度 |
| `computeVNDF` | [16,16,1] | 计算可见法线分布 |
| `acquireBRDF` | [16,16,1] | 执行 BRDF 测量 |

## 依赖关系

### import
- `Scene.Shading`
- `Utils.Math.MathHelpers`
- `Utils.Color.ColorHelpers`
- `Rendering.Materials.RGLCommon`
- `Utils.Sampling.UniformSampleGenerator`
