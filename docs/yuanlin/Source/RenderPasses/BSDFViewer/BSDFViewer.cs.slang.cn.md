# BSDFViewer.cs.slang 源码文档

> 路径: `Source/RenderPasses/BSDFViewer/BSDFViewer.cs.slang`
> 类型: 计算着色器 (Slang)
> 模块: RenderPasses/BSDFViewer

## 功能概述

BSDFViewer 的计算着色器实现。以 ParameterBlock 形式组织，包含双向散射分布函数切片查看和材质球渲染两种模式的完整实现。支持重要性采样、方向光、环境贴图光照以及像素级数据回传。

## 结构体与接口

### `BSDFViewer`（ParameterBlock）

主着色器结构体，作为 ParameterBlock 绑定。

| 字段 | 类型 | 说明 |
|------|------|------|
| `params` | `BSDFViewerParams` | 共享参数 |
| `envMap` | `EnvMap` | 环境贴图 |
| `outputColor` | `RWTexture2D<float4>` | 输出颜色纹理 |
| `pixelData` | `RWStructuredBuffer<PixelData>` | 像素数据回传缓冲区 |

### `SurfaceData`（内部结构）

| 字段 | 类型 | 说明 |
|------|------|------|
| `sd` | `ShadingData` | 着色数据 |
| `bsdfProperties` | `BSDFProperties` | 双向散射分布函数属性 |
| `wo` | `float3` | 出射方向（光源方向） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float2 getViewportCoord(uint2)` | 获取归一化视口坐标 |
| `float3 calculateSliceGeometry(float2, out VertexData, out float3)` | 计算双向散射分布函数切片几何（theta_h, theta_d 参数化） |
| `bool calculateSphereGeometry(float2, out VertexData, out float3)` | 计算单位球表面几何（支持正交和透视相机） |
| `SurfaceData prepareShadingData(VertexData, float3)` | 准备着色数据，设置活跃的双向散射分布函数叶瓣 |
| `float3 evalBackground(float2, float3)` | 评估背景颜色（地面平面、环境贴图或纯色） |
| `float3 evalLighting(float3)` | 评估给定方向的入射光照 |
| `uint getActiveLobes()` | 获取当前启用的双向散射分布函数叶瓣掩码 |
| `float3 evalBSDFSlice(float2, ITextureSampler, inout SurfaceData, inout SampleGenerator)` | 评估双向散射分布函数切片值 |
| `bool generateBSDFSample(ShadingData, IMaterialInstance, inout SampleGenerator, out BSDFSample)` | 生成双向散射分布函数样本（方向光或重要性采样） |
| `float3 getAlbedo(BSDFProperties)` | 根据选择标志计算总反照率 |
| `float3 evalSphere(float2, ITextureSampler, inout SurfaceData, inout SampleGenerator)` | 评估光照球体（材质模式主函数） |
| `void execute(uint2)` | 入口函数，根据模式调用切片或球体评估 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Shading` — 场景着色
- `Scene.Material.ShadingUtils` — 着色工具
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Utils.Debug.PixelDebug` — 像素调试
- `Utils.Math.BitTricks` — 位操作
- `Utils.Math.MathHelpers` — 数学辅助
- `Utils.Geometry.IntersectionHelpers` — 几何交叉辅助（球体求交）
- `BSDFViewerParams` — 参数定义

## 实现细节

- **切片参数化**: 使用 Burley et al. 2012/2015 的 (theta_h, theta_d) 参数化，theta_h 为半角和法线的夹角，theta_d 为半角和入射/出射方向的夹角。
- **球体渲染**: 使用解析单位球，正交模式下直接求解球面方程，透视模式下使用光线-球体求交。
- **纹理坐标**: 球体模式下使用柱面映射计算纹理坐标，也支持固定纹理坐标模式。
- **线程组**: 使用 16x16 线程组执行计算着色器。
- **数据回传**: 当像素坐标匹配选定像素时，将完整的着色数据、双向散射分布函数属性写入结构化缓冲区。
