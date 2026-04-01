# TextureSampler.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/TextureSampler.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
定义了纹理采样接口 `ITextureSampler` 及多种实现，支持不同的 LOD（细节层次）计算技术。这些采样器在材质系统中被广泛使用，决定纹理采样时的模糊/清晰程度。

## 结构体与接口

### `ITextureSampler` 接口
| 方法 | 说明 |
|------|------|
| `float4 sampleTexture(Texture2D t, SamplerState s, float2 uv)` | 使用该方法计算的 LOD 采样 2D 纹理 |

### 实现类

| 类名 | 说明 |
|------|------|
| `ImplicitLodTextureSampler` | 使用隐式梯度（仅像素着色器，SM 6.6 起支持其他阶段） |
| `ExplicitLodTextureSampler` | 使用显式标量 LOD 值 |
| `ExplicitRayConesLodTextureSampler` | 基于光线锥的 LOD 计算（纹理尺寸无关） |
| `ExplicitRayDiffsIsotropicTextureSampler` | 基于光线微分的各向同性 LOD |
| `ExplicitGradientTextureSampler` | 使用显式屏幕空间梯度（各向异性过滤） |
| `StochasticTextureSampler` | 随机纹理采样（支持 UV 抖动、LOD 抖动和各向异性控制） |

## 依赖关系
### 被以下文件引用
- `MaterialSystem.slang` - 纹理采样
- `MaterialFactory.slang` - 材质实例化
