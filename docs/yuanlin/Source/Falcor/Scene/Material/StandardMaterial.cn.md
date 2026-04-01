# StandardMaterial 源码文档
> 路径: `Source/Falcor/Scene/Material/StandardMaterial.h` + `StandardMaterial.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了 Falcor 的标准 PBR 材质，支持 MetalRough（金属度/粗糙度）和 SpecGloss（高光/光泽度）两种着色模型。这是最常用的材质类型，支持完整的纹理通道布局，包括基础颜色、高光参数、法线贴图、自发光和透射纹理。支持可微渲染的参数序列化/反序列化。

## 类与接口

### `StandardMaterial`
- **继承**: `BasicMaterial`
- **职责**: 标准 PBR 材质实现，支持两种着色模型

#### 纹理通道布局
**MetalRough 模式:**
- BaseColor: RGB=基础颜色, A=不透明度
- Specular: R=未使用, G=粗糙度, B=金属度, A=未使用

**SpecGloss 模式:**
- BaseColor: RGB=漫反射颜色, A=不透明度
- Specular: RGB=高光颜色, A=光泽度

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<StandardMaterial> create(...)` | 工厂方法，创建标准材质实例 |
| `ShadingModel getShadingModel() const` | 获取当前着色模型 |
| `void setRoughness(float)` | 设置粗糙度（仅 MetalRough 模式） |
| `void setMetallic(float)` | 设置金属度（仅 MetalRough 模式） |
| `void setEmissiveColor(const float3&)` | 设置自发光颜色 |
| `void setEmissiveFactor(float)` | 设置自发光强度因子 |
| `void setHasEntryPointVolumeProperties(bool)` | 启用纹理化的体积吸收系数 |
| `SerializedMaterialParams serializeParams() const` | 序列化材质参数（用于可微渲染） |
| `void deserializeParams(const SerializedMaterialParams&)` | 反序列化材质参数 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| 继承自 `BasicMaterial` | `BasicMaterialData mData` | 材质参数数据 |

## 依赖关系
### 本文件引用
- `BasicMaterial.h` - 基础材质基类
- `StandardMaterialParamLayout.slang` - 参数布局定义
- `Rendering/Materials/StandardMaterial.slang` - GPU 端着色器实现

### 被以下文件引用
- `MaterialSystem.cpp` - 用于检测 SpecGloss 模式
- `MaterialTypeRegistry.cpp` - 参数布局注册

## 实现细节
- `updateDeltaSpecularFlag()` 检测材质是否仅有 delta 高光反射/透射（用于跳过 NEE 优化）
- 着色器类型一致性声明将 `StandardMaterial` 映射到 `IMaterial` 接口
- 参数序列化仅支持 MetalRough 模式，包含 baseColor、metallic、roughness、ior 等参数
- Python 绑定中 `Material` 作为 `StandardMaterial` 的别名暴露，兼容旧版脚本
