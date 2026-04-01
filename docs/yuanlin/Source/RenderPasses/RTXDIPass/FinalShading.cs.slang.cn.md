# FinalShading.cs.slang 源码文档

> 路径: `Source/RenderPasses/RTXDIPass/FinalShading.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/RTXDIPass

## 功能概述

该计算着色器执行 RTXDI 渲染通道的最终着色阶段。从 ReSTIR 采样器获取最终光源采样，进行可见性光线追踪检查，然后使用完整的材质 BSDF 模型分别评估漫反射和镜面反射分量，并支持颜色解调（demodulation）输出。

## 结构体与接口

### `FinalShading`

- **职责**: 封装最终着色逻辑

| 成员 | 类型 | 说明 |
|------|------|------|
| `kUseEnvBackground` | `static const bool` | 是否使用环境贴图背景（编译时常量） |
| `vbuffer` | `Texture2D<PackedHitInfo>` | 可见性缓冲区 |
| `frameDim` | `uint2` | 帧尺寸 |

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `void execute(const uint2 pixel)` | 对单个像素执行完整着色流程 |

### 输出纹理

| 名称 | 类型 | 说明 |
|------|------|------|
| `gColor` | `RWTexture2D<float4>` | 最终颜色（漫反射 + 镜面反射 + 自发光） |
| `gEmission` | `RWTexture2D<float4>` | 自发光颜色 |
| `gDiffuseIllumination` | `RWTexture2D<float4>` | 解调后的漫反射光照（alpha 通道为 hitT） |
| `gDiffuseReflectance` | `RWTexture2D<float4>` | 漫反射反射率 |
| `gSpecularIllumination` | `RWTexture2D<float4>` | 解调后的镜面反射光照（alpha 通道为 hitT） |
| `gSpecularReflectance` | `RWTexture2D<float4>` | 镜面反射反射率 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 dispatchThreadId: SV_DispatchThreadID)` | 计算着色器入口，线程组大小 [16, 16, 1] |

## 依赖关系/import

| 模块 | 说明 |
|------|------|
| `LoadShadingData` | 加载和准备着色数据 |
| `Scene.RaytracingInline` | DXR 1.1 内联光线追踪 |
| `Rendering.Materials.IsotropicGGX` | 各向同性 GGX 镜面反射模型 |
| `Rendering.RTXDI.RTXDI` | RTXDI 模块 |
| `Utils.Sampling.TinyUniformSampleGenerator` | 轻量级均匀采样生成器 |

## 实现细节

1. **着色流程**: 加载着色数据 -> 获取 RTXDI 最终采样 -> 可见性检查 -> 分别评估漫反射和镜面反射 BSDF -> 颜色解调 -> 写入输出

2. **可见性检查**: 使用 DXR 1.1 `SceneRayQuery<1>`（启用 alpha 测试）追踪阴影光线验证光源可见性。

3. **颜色解调**: 漫反射使用 `diffuseReflectionAlbedo` 解调，镜面反射使用 `approxSpecularIntegralGGX` 预积分的镜面反射率解调。这种拆分允许后续通道（如降噪器）分别处理光照和反射率。

4. **背景像素处理**: 对于未命中任何几何体的背景像素，如果启用了环境贴图背景，则从环境贴图采样颜色。

5. **输出通道验证**: 使用 `is_valid(name)` 宏检查输出通道是否已绑定，仅写入有效的输出通道。
