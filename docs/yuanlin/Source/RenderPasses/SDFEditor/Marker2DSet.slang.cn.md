# Marker2DSet.slang 源码文档

> 路径: `Source/RenderPasses/SDFEditor/Marker2DSet.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/SDFEditor

## 功能概述

2D 标记集渲染的着色器实现。为 `Marker2DDataBlob` 提供 `extension` 方法，支持在 GPU 端根据标记类型渲染各种 2D SDF 形状（圆形、方形、菱形、心形、箭头、圆角矩形、圆角线、三角形、向量、操作标记、圆弧扇区等），使用有符号距离场进行抗锯齿混合。

## 结构体与接口

### `Marker2DDataBlob` (extension)

为 `Marker2DDataBlob` 添加的着色器端方法。

### `Marker2DSet`

GPU 端标记集结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `markers` | `StructuredBuffer<Marker2DDataBlob>` | 标记数据缓冲区 |
| `markerCount` | `uint` | 标记数量 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 render(uint2, float4, float)` | 根据标记类型渲染并返回颜色 |
| `float4 renderMarkerOpMarker(uint2, float4, float)` | 渲染两个标记及其布尔操作 |
| `float4 renderArrowFromTwoTris(uint2, float4, float)` | 渲染由两个三角形组成的箭头 |
| `float4 renderCircleSector(uint2, float4, float)` | 渲染圆弧扇区（含边框） |
| `float4 sdfDrawSimpleMarker<SDF>(...)` | 泛型辅助函数，绘制简单标记 |
| `void calcDistanceandAndBlendFactorFromBasicMarker(...)` | 计算基本标记的有符号距离场距离和混合因子 |
| `float4 Marker2DSet::render(uint2, float4)` | 遍历所有标记并逐一渲染 |

## 依赖关系 / import

- `Marker2DTypes` — 标记类型定义
- `Utils.SDF.SDF2DDraw` — 2D SDF 绘制工具
- `Utils.SDF.SDF2DPrimitives` — 2D SDF 基元（圆形、方形等）
- `Utils.SDF.SDFOperations` — SDF 布尔运算
- `Utils.Math.MathHelpers` — 数学辅助
