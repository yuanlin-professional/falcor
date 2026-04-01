# StandardMaterialParamLayout.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/StandardMaterialParamLayout.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
使用 `DEFINE_MATERIAL_PARAM_LAYOUT` 宏定义标准材质（MetalRough 模式）的可微渲染参数布局。定义了 baseColor、metallic、roughness、ior、transmissionColor 等 9 个可微参数的偏移量、访问器和 Python 名称。

## 结构体与接口

### `StandardMaterialParamLayout`（由宏生成）
提供：
- `static const MaterialParamLayout& layout()` - 获取参数布局
- `static SerializedMaterialParams serialize(const StandardMaterial*)` - 序列化
- `static void deserialize(StandardMaterial*, const SerializedMaterialParams&, bool clamp)` - 反序列化

### 参数列表
| 偏移量 | 类型 | 名称 | Python 名称 | 说明 |
|--------|------|------|-------------|------|
| 0 | float3 | baseColor | base_color | 基础颜色 |
| 3 | float | metallic | metallic | 金属度 |
| 4 | float | roughness | roughness | 粗糙度（钳制下限 0.05） |
| 5 | float | ior | ior | 折射率 |
| 6 | float3 | transmissionColor | transmission_color | 透射颜色 |
| 9 | float | diffuseTransmission | diffuse_transmission | 漫反射透射 |
| 10 | float | specularTransmission | specular_transmission | 高光透射 |
| 11 | float3 | emissiveColor | emissive_color | 自发光颜色 |
| 14 | float | emissiveFactor | emissive_factor | 自发光因子 |

## 依赖关系
### import
- `MaterialParamLayout.slangh` - 宏定义系统
