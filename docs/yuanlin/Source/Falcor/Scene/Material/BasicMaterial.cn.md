# BasicMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/BasicMaterial.h` + `BasicMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
定义了基础非分层材质的基类 `BasicMaterial`，继承自 `Material`。提供了标准材质、布料材质和头发材质共享的通用功能，包括基础颜色、高光参数、透射、体积散射/吸收、位移贴图等属性的管理。该类是 Falcor 材质层次结构中的关键中间层。

## 类与接口

### `BasicMaterial`
- **继承**: `Material`
- **职责**: 所有基础非分层材质的公共基类，管理纹理槽位、材质参数和位移贴图

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `bool renderUI(Gui::Widgets& widget)` | 渲染材质 UI，包括基础颜色、高光、法线贴图等编辑控件 |
| `UpdateFlags update(MaterialSystem* pOwner)` | 更新材质状态，上传纹理句柄和采样器到 GPU |
| `bool isDisplaced() const` | 判断是否使用了位移贴图 |
| `bool isEqual(const ref<Material>& pOther) const` | 比较两个 BasicMaterial 是否相同 |
| `void setBaseColor(const float4& color)` | 设置基础颜色（RGB + 不透明度） |
| `void setSpecularParams(const float4& color)` | 设置高光参数 |
| `void setTransmissionColor(const float3& color)` | 设置透射颜色 |
| `void setDiffuseTransmission(float)` | 设置漫反射透射量 |
| `void setSpecularTransmission(float)` | 设置高光透射量 |
| `void setVolumeAbsorption(const float3&)` | 设置体积吸收系数 |
| `void setVolumeScattering(const float3&)` | 设置体积散射系数 |
| `void setVolumeAnisotropy(float)` | 设置体积相函数各向异性参数 (g) |
| `void setDisplacementScale(float)` | 设置位移缩放 |
| `void setDisplacementOffset(float)` | 设置位移偏移 |
| `void optimizeTexture(TextureSlot, TextureAnalyzer::Result&, TextureOptimizationStats&)` | 优化纹理使用，将常量纹理替换为统一参数 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mData` | `BasicMaterialData` | 材质参数数据结构 |
| `mpDefaultSampler` | `ref<Sampler>` | 默认纹理采样器 |
| `mpDisplacementMinSampler` | `ref<Sampler>` | 位移贴图最小值采样器 |
| `mpDisplacementMaxSampler` | `ref<Sampler>` | 位移贴图最大值采样器 |
| `mAlphaRange` | `float2` | Alpha 值的保守范围 |

## 依赖关系
### 本文件引用
- `Material.h` - 材质基类
- `BasicMaterialData.slang` - 基础材质数据结构
- `MaterialSystem.h` - 材质系统

### 被以下文件引用
- `StandardMaterial.h` - 标准材质
- `ClothMaterial.h` - 布料材质
- `HairMaterial.h` - 头发材质

## 实现细节
- 构造函数设置 `isBasicMaterial` 标志为 true，默认折射率为 1.5
- `update()` 方法依次更新所有纹理句柄（基础颜色、高光、自发光、透射、法线、位移）和采样器 ID
- `prepareDisplacementMapForRendering()` 将位移贴图转换为 RGBA16Float 格式，并生成包含平均/最小/最大值的 MIP 金字塔
- `optimizeTexture()` 分析纹理内容，将常量纹理替换为统一材质参数以提升性能
- `updateAlphaMode()` 根据 Alpha 阈值和 Alpha 范围自动决定是否启用 Alpha 测试
- `adjustDoubleSidedFlag()` 当使用透射或位移时自动设置双面渲染
- 体积各向异性参数被限制在 [-0.99, 0.99] 范围内
