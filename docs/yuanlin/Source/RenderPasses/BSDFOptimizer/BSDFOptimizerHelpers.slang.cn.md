# BSDFOptimizerHelpers.slang 源码文档

> 路径: `Source/RenderPasses/BSDFOptimizer/BSDFOptimizerHelpers.slang`
> 类型: Slang 着色器工具库
> 模块: RenderPasses/BSDFOptimizer

## 功能概述

BSDFOptimizer 的着色器辅助函数库。提供视口坐标转换和双向散射分布函数切片几何计算功能，被优化器通道和查看器通道共同使用。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float2 getViewportCoord(uint2 pixel, float2 viewportOffset, float2 viewportScale)` | 将像素坐标转换为 [0,1] 视口坐标 |
| `float3 calculateSliceGeometry(float2 uv, out VertexData v, out float3 viewDir)` | 计算双向散射分布函数切片的入射/出射方向几何 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量（M_PI）
- `Scene.Shading` — 场景着色（VertexData 类型）

## 实现细节

- **切片参数化**: 与 BSDFViewer 使用相同的 Burley et al. 2012/2015 参数化方式：
  - X 轴: theta_h（半角与法线的夹角），范围 [0, pi/2]
  - Y 轴: theta_d（半角与入射/出射方向的夹角），范围 [0, pi/2]
  - 原点在左下角
- **几何构建**: 先将半角向量 H 放在 (0,0,1)，计算关于 H 对称的入射/出射方向 L 和 V，然后绕 X 轴旋转 theta_h 角度。
- **顶点数据**: 假定无纹理（texCoords = {0,0}），法线沿 Z 轴，切线沿 X 轴。
