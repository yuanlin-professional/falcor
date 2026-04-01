# MERLMixMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/MERLMixMaterial.h` + `MERLMixMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了 MERL BRDF 混合材质。该类加载一组 MERL BRDF 数据，允许在运行时通过索引纹理在多个 BRDF 之间进行混合，从而创建空间变化的测量 BRDF 马赛克效果。支持法线贴图。

## 类与接口

### `MERLMixMaterial`
- **继承**: `Material`
- **职责**: 支持多 BRDF 混合的测量材质

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<MERLMixMaterial> create(...)` | 工厂方法，接受 BRDF 路径列表 |
| `bool setTexture(TextureSlot, const ref<Texture>&)` | 设置法线贴图或索引纹理 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mBRDFs` | `vector<BRDFDesc>` | 已加载 BRDF 列表 |
| `mpBRDFData` | `ref<Buffer>` | GPU 缓冲区，存储所有 BRDF 数据 |
| `mpAlbedoLUT` | `ref<Texture>` | 2D 反照率查找表（cosTheta x brdfIndex） |
| `mpIndexSampler` | `ref<Sampler>` | 索引纹理采样器（点采样） |

## 依赖关系
### 本文件引用
- `Material.h`, `MERLMixMaterialData.slang`, `MERLFile.h`
- `Utils/BufferAllocator.h` - 缓冲区分配器

## 实现细节
- 所有 BRDF 数据打包到单个缓冲区，使用等间距步长排列
- 索引纹理必须为非压缩 8 位 unorm 格式（R8、RG8、RGBA8 等），BRDF 索引通过 unorm 值乘以 255 计算
- 反照率 LUT 为 2D 纹理，参数化为 (cosTheta, brdfIndex)
- DiffuseSpecular 拟合参数也存储在缓冲区中用于采样优化
