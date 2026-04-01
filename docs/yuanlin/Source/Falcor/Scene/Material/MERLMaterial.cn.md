# MERLMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/MERLMaterial.h` + `MERLMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了基于 MERL BRDF 数据库的测量材质。该材质类型使用从实际物理材料采集的双向散射分布函数（BSDF）数据进行渲染，能够精确还原真实材料的外观。

参考文献：Wojciech Matusik 等, "A Data-Driven Reflectance Model", ACM TOG, 2003.

## 类与接口

### `MERLMaterial`
- **继承**: `Material`
- **职责**: 基于测量数据的 MERL BRDF 材质

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<MERLMaterial> create(...)` | 工厂方法 |
| `UpdateFlags update(MaterialSystem* pOwner)` | 更新缓冲区和纹理句柄到材质系统 |
| `size_t getMaxBufferCount() const` | 返回 1（使用一个缓冲区存储 BRDF 数据） |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mPath` | `path` | BRDF 文件完整路径 |
| `mData` | `MERLMaterialData` | 材质参数 |
| `mpBRDFData` | `ref<Buffer>` | GPU 缓冲区，存储 BRDF 数据 |
| `mpAlbedoLUT` | `ref<Texture>` | 预计算的反照率查找表纹理 |

## 依赖关系
### 本文件引用
- `Material.h` - 材质基类
- `MERLMaterialData.slang` - 材质数据结构
- `MERLFile.h` - MERL 文件加载
- `Rendering/Materials/MERLMaterial.slang` - GPU 端着色器

## 实现细节
- BRDF 数据作为 float3 数组存储在 GPU 缓冲区中
- 反照率查找表为 256 元素的 1D 纹理（RGBA32Float 格式）
- `update()` 中注册缓冲区和采样器到材质系统，获取 ID 用于着色器访问
- 支持 DiffuseSpecular 近似参数用于采样优化
