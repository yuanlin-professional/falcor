# GBufferHelpers.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferHelpers.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/GBuffer

## 功能概述

提供 G-buffer 生成的辅助函数和数据结构。定义了 `GBufferData` 结构体用于存储所有 G-buffer 通道数据，以及 `prepareGBufferData` 函数用于从着色数据和材质属性中准备 G-buffer 数据。

## 结构体与接口

### `GBufferData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `posW` | `float4` | 世界空间位置 |
| `normW` | `float4` | 着色法线 |
| `tangentW` | `float4` | 切线和手性 |
| `faceNormalW` | `float4` | 面法线 |
| `guideNormalW` | `float4` | 引导法线 |
| `texC` | `float2` | 纹理坐标 |
| `mtlData` | `uint4` | 材质数据 |
| `diffuseOpacity` | `float4` | 漫反射反照率和不透明度 |
| `specRough` | `float4` | 镜面反射率和粗糙度 |
| `emissive` | `float4` | 发射颜色 |

## 函数

| 函数 | 说明 |
|------|------|
| `prepareGBufferData(ShadingData, VertexData, IMaterialInstance, BSDFProperties)` | 从着色数据准备完整的 G-buffer 数据 |

## 依赖关系 / import

- `Scene.SceneTypes`, `Scene.ShadingData` — 场景类型
- `Rendering.Materials.IMaterialInstance` — 材质实例接口
- `Utils.Math.MathHelpers`, `Utils.Math.FormatConversion` — 工具函数
- `Scene.Material.ShadingUtils` — 着色工具
