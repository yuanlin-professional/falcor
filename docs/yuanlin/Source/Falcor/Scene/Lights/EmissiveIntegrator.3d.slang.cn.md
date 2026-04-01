# EmissiveIntegrator.3d.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/EmissiveIntegrator.3d.slang`
> 类型: Slang 3D 着色器（顶点 + 几何 + 像素着色器）
> 模块: Scene/Lights

## 功能概述

光栅化着色器程序，用于对纹理化的自发光三角形进行积分。将自发光三角形投射到纹理空间中光栅化，每个纹素产生一个像素着色器执行。使用保守光栅化确保部分覆盖的纹素也被处理。

积分过程分两遍：
1. **第一遍（INTEGRATOR_PASS=1）**：确定每个三角形的最大纹素值，使用 fp32 原子最大值操作。
2. **第二遍（INTEGRATOR_PASS=2）**：将纹素值归一化后累加，使用 64 位定点格式的原子加法操作，保证结果与光栅化顺序无关。

## 结构体与接口

### `GsOut`

| 成员 | 语义 | 说明 |
|------|------|------|
| `uv` | TexCoord | 纹理坐标 |
| `texelPos` | TexelPosition | 纹素空间位置 |
| `posH` | SV_Position | 齐次裁剪空间位置 |
| `triIdx` | TriangleIndex | 三角形索引（不插值） |
| `materialID` | MaterialID | 材质 ID（不插值） |
| `vtxTexelPos[3]` | VertexTexelPositions | 三个顶点的纹素空间位置（不插值） |

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gLightCollection` | `ParameterBlock<LightCollection>` | 光源集合数据 |
| `gTexelMax` | `RWByteAddressBuffer` | 最大纹素值（fp32 原子） |
| `gTexelSum` | `RWByteAddressBuffer` | 纹素累加值（64 位定点原子） |
| `gPointSampler` | `SamplerState` | 最近邻采样器 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void vsMain()` | 空顶点着色器 |
| `void gsMain(uint triIdx, inout TriangleStream<GsOut>)` | 几何着色器：将三角形投射到纹理空间，剔除非纹理化三角形 |
| `void psMain(GsOut gsIn, uint innerCoverage)` | 像素着色器：采样纹素并累加/求最大值 |

## 依赖关系 / import

- `Utils.Geometry.GeometryHelpers` — 三角形裁剪面积计算
- `Scene.Scene` — 场景和材质访问

## 实现细节

- 视口大小为 16K x 16K，不绑定渲染目标。
- 几何着色器将三角形从纹理坐标空间变换到 NDC 空间，每纹素一个像素。
- 第二遍使用 `SV_InnerCoverage` 判断纹素是否完全覆盖，部分覆盖的纹素通过 `computeClippedTriangleArea2D` 解析计算覆盖面积。
- 累加使用 29.35 位定点格式（29 位整数部分足以表示 16K x 16K 视口上 1.0 的累加和）。
