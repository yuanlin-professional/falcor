# PolymorphicLight.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/PolymorphicLight.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/RTXDI

## 功能概述

本文件实现了 RTXDI 使用的多态光源系统。它定义了统一的光源接口 `IPolymorphicLight`，以及三种具体的光源类型实现：发光三角形光源（`EmissiveLight`）、解析光源（`AnalyticLight`）和环境光（`EnvLight`）。每种光源都能打包为统一的 `PackedPolymorphicLight` 格式，并支持通量计算和光源采样。

采样功能通过条件编译宏 `RTXDI_POLYMORPHIC_LIGHT_SAMPLING` 控制，仅在 `RTXDI.slang` 中启用。

## 结构体与接口

### 枚举 `PolymorphicLightType`

| 值 | 说明 |
|----|------|
| `Emissive` | 发光三角形光源 |
| `Analytic` | 解析光源（点光源、方向光等） |
| `Env` | 环境光 |

### `PackedPolymorphicLight` extension

为打包类型添加头部操作辅助方法。

| 函数 | 说明 |
|------|------|
| `setHeader(type, extra)` | 设置光源类型和附加位 |
| `getType() -> PolymorphicLightType` | 获取光源类型 |
| `getExtra() -> uint` | 获取附加数据位 |

### `PolymorphicLightSample`

光源采样结果结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `position` | `float3` | 光源上的世界空间位置 |
| `normal` | `float3` | 光源面法线 |
| `direction` | `float3` | 从着色点到光源的方向 |
| `distance` | `float` | 到光源的距离 |
| `Li` | `float3` | 入射辐射度（未遮挡，预除 PDF） |
| `pdf` | `float` | 相对立体角的 PDF |
| `lightType` | `PolymorphicLightType` | 光源类型属性 |

### `IPolymorphicLight` 接口

| 方法 | 说明 |
|------|------|
| `pack() -> PackedPolymorphicLight` | 打包为通用类型 |
| `getFlux() -> float` | 计算总通量（无限远光源返回 0） |
| `calcSample(position, u, scalarLi) -> PolymorphicLightSample` | 计算光源采样（条件编译） |

### `EmissiveLight`

发光三角形光源实现。

| 成员 | 说明 |
|------|------|
| `triIdx` | 全局三角形索引 |
| `position` | 顶点 0 的世界坐标 |
| `edge0`, `edge1` | 三角形两条边（half-precision 存储） |
| `averageRadiance` | 标量平均辐射度 |

- 通量 = 面积 * 平均辐射度 * PI（假设漫反射发光）
- 采样使用均匀三角形面积采样（折叠随机数法）

### `AnalyticLight`

解析光源实现，存储场景光源 ID 的引用。

- 支持点光源、方向光和远距光
- 通量仅对点光源计算（2PI * (1 - cosOpeningAngle) * luminance）

### `EnvLight`

环境光实现。

- 通量返回 0（无限远光源不贡献局部通量）
- 采样将 UV 坐标转换为世界方向，查询环境贴图并除以 PDF

### `PolymorphicLight`

多态分发包装器，根据类型动态调用具体实现。

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `Scene.Scene`
- `Rendering.Lights.LightHelpers`（解析光源采样函数）
- `Utils.Math.MathHelpers`
- `Utils.Color.ColorHelpers`
- `PackedTypes`（`__exported import`，导出打包类型）

## 实现细节

- 发光三角形的边向量使用 FP16 存储以节省空间，头部附加位存储 `triIdx`
- 解析光源头部附加位存储 `lightID`
- 环境光的 PDF = 1 / (2 * PI^2 * cos(theta))，theta 为纬度角
- 当 `useEmissiveTextures` 为 true 时，发光三角形查询材质纹理获取精确发射辐射度；否则使用预积分平均值
