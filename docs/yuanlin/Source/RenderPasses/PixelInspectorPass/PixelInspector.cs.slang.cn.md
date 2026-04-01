# PixelInspector.cs.slang 源码文档

> 路径: `Source/RenderPasses/PixelInspectorPass/PixelInspector.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/PixelInspectorPass

## 功能概述

像素检查器的计算着色器实现。在选中像素位置加载 G-Buffer 数据，准备着色数据，查询材质实例属性，并将结果写入 `PixelData` 结构化缓冲区供 CPU 端读取显示。

## 结构体与接口

### 常量缓冲区 `PerFrameCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gResolution` | `uint2` | 输出分辨率 |
| `gSelectedPixel` | `uint2` | 选中像素坐标 |
| `g*Coord` | `uint2` | 各输入纹理的采样坐标（支持缩放） |

### 输入纹理

| 纹理 | 说明 |
|------|------|
| `gWorldPosition` | 世界空间位置 |
| `gWorldShadingNormal` | 世界空间着色法线 |
| `gWorldTangent` | 世界空间切线 |
| `gWorldFaceNormal` | 世界空间面法线 |
| `gTextureCoord` | 纹理坐标 |
| `gTextureGrads` | 纹理梯度 |
| `gMaterialData` | 材质数据 |
| `gLinearColor` | 线性颜色（色调映射前） |
| `gOutputColor` | 输出颜色（色调映射后） |
| `gVBuffer` | 可见性缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `ITextureSampler createTextureSampler(uint2)` | 创建纹理采样器（支持梯度或显式 LOD） |
| `bool loadShadingData(float3, inout ShadingData)` | 从 G-Buffer 加载着色数据 |
| `[numthreads(1,1,1)] void main(uint3)` | 主入口，加载数据并写入 PixelData |

## 依赖关系 / import

- `Scene.Shading` — 着色数据
- `Scene.Camera.Camera` — 相机
- `Utils.Color.ColorHelpers` — 颜色辅助（luminance）
- `PixelInspectorData` — PixelData 结构体定义

## 实现细节

- 使用针孔相机模型计算视线方向（不支持景深）
- 通过 `gWorldPosition.w != 0` 判断有效几何像素
- 加载材质实例后查询 BSDF 属性（emission、roughness、guideNormal、各反照率等）
- VBuffer 解码获取命中类型、实例 ID、图元索引和重心坐标
