# PrepareSurfaceData.cs.slang 源码文档

> 路径: `Source/RenderPasses/RTXDIPass/PrepareSurfaceData.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/RTXDIPass

## 功能概述

该计算着色器是 RTXDI 渲染通道的第一阶段，负责从可见性缓冲区（VBuffer）中提取表面属性数据，并将其提供给 RTXDI 模块用于光源重要性重采样。它使用简化的材质模型（仅漫反射和镜面反射），将表面位置、引导法线、反照率和粗糙度等信息传递给 RTXDI。

## 结构体与接口

### `PrepareSurfaceData`

- **职责**: 封装表面数据准备逻辑

| 成员 | 类型 | 说明 |
|------|------|------|
| `vbuffer` | `Texture2D<PackedHitInfo>` | 可见性缓冲区 |
| `texGrads` | `Texture2D<float2>` | 纹理梯度（未使用，预留） |
| `frameDim` | `uint2` | 帧尺寸 |

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `void execute(const uint2 pixel)` | 对单个像素提取表面数据并传递给 RTXDI |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 dispatchThreadId: SV_DispatchThreadID)` | 计算着色器入口，线程组大小 [16, 16, 1] |

## 依赖关系/import

| 模块 | 说明 |
|------|------|
| `LoadShadingData` | 加载着色数据（共享模块） |
| `Utils.Color.ColorHelpers` | 颜色辅助函数 |
| `Rendering.RTXDI.RTXDI` | RTXDI 模块接口 |

## 实现细节

1. **有效表面处理**: 对于有效像素，创建材质实例并查询 BSDF 属性，然后调用 `gRTXDI.setSurfaceData()` 传递表面原点、引导法线、漫反射反照率、镜面反射率和粗糙度。

2. **无效表面处理**: 对于背景像素，调用 `gRTXDI.setInvalidSurfaceData()` 标记为无效。

3. **简化材质模型**: RTXDI 在重采样阶段使用简化模型（漫反射反照率亮度和镜面反射率亮度作为权重），最终着色阶段才使用完整的材质模型。这是为了在保持重采样效率的同时不牺牲最终着色质量。

4. **纹理 LOD**: 当前使用 `ExplicitLodTextureSampler(0.f)` 作为纹理采样 LOD，未实现完整的纹理细节级别计算。
