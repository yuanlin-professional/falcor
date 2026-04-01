# LightHelpers.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightHelpers.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件提供了对 Falcor 解析光源（Analytic Light）的采样和评估辅助函数。支持点光源（聚光灯）、方向光、矩形面光源、球形面光源、圆盘面光源和远距离光源。每种光源类型有独立的采样函数，以及一个统一的调度函数 `sampleLight` 根据光源类型自动选择正确的采样方法。注意：网格光源（自发光几何体）和光照探针由其他模块处理。

## 结构体与接口

### `AnalyticLightSample`

- **职责**: 描述解析光源的采样结果

| 成员 | 类型 | 说明 |
|------|------|------|
| `posW` | `float3` | 光源上采样点的世界坐标（仅局部光源） |
| `normalW` | `float3` | 采样点的世界空间法线（归一化） |
| `dir` | `float3` | 从着色点到光源采样点的归一化方向 |
| `distance` | `float` | 着色点到采样点的距离 |
| `Li` | `float3` | 着色点处的入射辐射度（未遮挡），已除以 PDF |
| `pdf` | `float` | 相对于立体角的概率密度（Dirac 光源为 0） |

### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kMinLightDistSqr` | 1e-9f | 最小距离平方，防止除零 |
| `kMaxLightDistance` | FLT_MAX | 方向光和远距离光的距离值 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `bool finalizeAreaLightSample(...)` | 内部辅助函数，完成面光源采样的通用计算（方向、距离、辐射度、PDF） |
| `bool sampleRectAreaLight(...)` | 采样矩形面光源，在 [-1,1]^2 对象空间中均匀采样后变换到世界空间 |
| `bool sampleSphereAreaLight(...)` | 采样球形面光源，在单位球上均匀采样后变换到世界空间 |
| `bool sampleDiscAreaLight(...)` | 采样圆盘面光源，在单位圆盘上均匀采样后变换到世界空间 |
| `bool sampleDistantLight(...)` | 采样远距离光源，在张角定义的锥内均匀采样方向 |
| `bool sampleDirectionalLight(...)` | 采样方向光，PDF 为 Dirac 函数（设为 0） |
| `bool samplePointLight(...)` | 采样点光源/聚光灯，计算衰减和聚光效果，PDF 为 Dirac 函数 |
| `bool sampleLight<S>(...)` | 统一采样入口，根据 `light.type` 调度到对应的采样函数 |
| `bool evalLightApproximate(...)` | 近似评估光源（仅支持点光和方向光），用于不使用随机积分的光栅化通道 |

## 实现细节

### 面光源采样流程

1. 在对象空间中生成随机采样点
2. 通过光源变换矩阵变换到世界空间（位置和法线）
3. 调用 `finalizeAreaLightSample` 计算方向、距离、背面检测、入射辐射度和 PDF

### 点光源/聚光灯

- 计算到光源的方向和距离
- 聚光灯：使用 `cosOpeningAngle` 和 `penumbraAngle` 计算 `smoothstep` 衰减
- 入射辐射度 = intensity * falloff / distSqr
- PDF = 0（Dirac 函数）

### 远距离光

- 使用 `sample_cone` 在张角锥内均匀采样方向
- 亮度按辐射度处理（不随距离衰减），PDF = 1 / 立体角

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Lights.LightData` — 光源数据结构
- `Utils.Math.MathHelpers` — 数学辅助函数
- `Utils.Sampling.SampleGeneratorInterface` — 样本生成器接口
