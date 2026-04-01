# MERLMaterialData.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MERLMaterialData.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了 MERL 测量材质的数据结构 `MERLMaterialData`，包含 BRDF 数据缓冲区 ID、采样器 ID、反照率查找表纹理句柄和漫反射/高光拟合参数。

## 结构体与接口

### `MERLMaterialData`
| 字段 | 类型 | 说明 |
|------|------|------|
| `bufferID` | `uint` | 材质系统中 BRDF 数据的缓冲区 ID |
| `samplerID` | `uint` | 查找表采样器 ID |
| `extraData` | `DiffuseSpecularData` | 最佳拟合 BRDF 近似参数 |
| `texAlbedoLUT` | `TextureHandle` | 反照率查找表纹理句柄 |
| `kAlbedoLUTSize` | `constexpr uint` | 查找表大小 = 256 |

## 依赖关系
### import
- `TextureHandle.slang`, `DiffuseSpecularData.slang`
