# MERLMixMaterialData.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MERLMixMaterialData.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了 MERL 混合材质的数据结构 `MERLMixMaterialData`，支持多个 BRDF 的混合渲染。包含 BRDF 计数、数据缓冲区步长、法线贴图和索引纹理句柄等。

## 结构体与接口

### `MERLMixMaterialData`
| 字段 | 类型 | 说明 |
|------|------|------|
| `flags` | `uint` | 材质标志和打包的采样器 ID |
| `brdfCount` | `uint` | 已加载 BRDF 数量 |
| `byteStride` | `uint` | 数据缓冲区中各 BRDF 之间的字节步长 |
| `bufferID` | `uint` | BRDF 数据缓冲区 ID |
| `extraDataOffset` | `uint` | 额外采样数据的字节偏移 |
| `extraDataStride` | `uint` | 额外数据的字节步长 |
| `texNormalMap` | `TextureHandle` | 法线贴图句柄 |
| `texIndexMap` | `TextureHandle` | 索引纹理句柄 |
| `texAlbedoLUT` | `TextureHandle` | 反照率查找表句柄 |

### 位域访问器
- `setNormalMapType/getNormalMapType` - 法线贴图类型
- `setLUTSamplerID/getLUTSamplerID` - LUT 采样器 ID
- `setIndexSamplerID/getIndexSamplerID` - 索引纹理采样器 ID

## 依赖关系
### import
- `TextureHandle.slang`, `MaterialTypes.slang`, `MaterialData.slang`
