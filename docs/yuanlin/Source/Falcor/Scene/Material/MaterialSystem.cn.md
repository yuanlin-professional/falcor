# MaterialSystem 源码文档
> 路径: `Source/Falcor/Scene/Material/MaterialSystem.h` + `MaterialSystem.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
材质系统的核心管理类，负责管理所有材质及其关联资源。提供材质的添加/删除/替换、纹理采样器管理、缓冲区管理、GPU 数据上传、着色器 define 生成以及类型一致性声明等功能。对应 GPU 端的 `MaterialSystem` Slang 结构体。

## 类与接口

### `MaterialSystem`
- **继承**: 无
- **职责**: 材质集合管理、GPU 资源分配、着色器配置

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `MaterialSystem(ref<Device>)` | 构造函数，创建纹理管理器和默认采样器 |
| `UpdateFlags update(bool forceUpdate)` | 更新所有材质，上传 GPU 数据 |
| `MaterialID addMaterial(const ref<Material>&)` | 添加材质，返回 ID |
| `void removeMaterial(MaterialID)` | 移除材质 |
| `void replaceMaterial(MaterialID, const ref<Material>&)` | 替换材质 |
| `void getDefines(DefineList&) const` | 获取着色器宏定义 |
| `void getTypeConformances(TypeConformanceList&) const` | 获取类型一致性列表 |
| `ProgramDesc::ShaderModuleList getShaderModules() const` | 获取着色器模块列表 |
| `void bindShaderData(const ShaderVar&) const` | 绑定材质系统参数块到着色器 |
| `uint32_t addTextureSampler(const ref<Sampler>&)` | 添加纹理采样器 |
| `uint32_t addBuffer(const ref<Buffer>&)` | 添加缓冲区资源 |
| `size_t removeDuplicateMaterials(vector<MaterialID>&)` | 移除重复材质 |
| `void optimizeMaterials()` | 优化材质，分析并替换常量纹理 |
| `MaterialStats getStats() const` | 获取材质系统统计信息 |
| `TextureManager& getTextureManager()` | 获取纹理管理器 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mMaterials` | `vector<ref<Material>>` | 所有材质列表 |
| `mpTextureManager` | `unique_ptr<TextureManager>` | 纹理管理器 |
| `mpMaterialsBlock` | `ref<ParameterBlock>` | GPU 参数块 |
| `mpMaterialDataBuffer` | `ref<Buffer>` | GPU 材质数据缓冲区 |
| `mTextureSamplers` | `vector<ref<Sampler>>` | 纹理采样器数组 |
| `mBuffers` | `vector<ref<Buffer>>` | 材质使用的缓冲区数组 |

## 依赖关系
### 本文件引用
- `Material.h` - 材质基类
- `StandardMaterial.h` - 标准材质（用于 SpecGloss 检测）
- `TextureManager.h` - 纹理管理
- `Scene/Lights/LightProfile.h` - 光照配置文件

### 被以下文件引用
- 所有材质类型的 `update()` 方法
- 场景系统 `Scene` 类

## 实现细节
- 材质数据以 128 字节的 `MaterialDataBlob` 结构上传到 GPU 结构化缓冲区
- 采样器通过描述符比较进行去重，最大数量由 `MaterialHeader::kSamplerIDBits` 决定
- 参数块使用着色器反射创建，包含材质数据、纹理数组、采样器数组、缓冲区数组
- `update()` 支持增量更新：仅全量更新时重建参数块，否则仅更新动态材质
- 着色器 define 包括描述符数量、UDIM 支持、SpecGloss 存在标志、光照配置文件等
- `optimizeMaterials()` 使用 `TextureAnalyzer` 在 GPU 上分析纹理内容，然后在 CPU 端应用优化
