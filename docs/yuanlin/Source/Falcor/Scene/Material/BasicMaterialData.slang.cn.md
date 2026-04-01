# BasicMaterialData.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/BasicMaterialData.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了基础材质的参数数据结构 `BasicMaterialData`，在主机端（C++）和设备端（GPU 着色器）之间共享。包含基础颜色、高光参数、透射、体积属性、位移贴图和纹理句柄等字段。

## 结构体与接口

### `BasicMaterialData`
| 字段 | 类型 | 说明 |
|------|------|------|
| `flags` | `uint` | 材质标志和打包的采样器 ID |
| `emissiveFactor` | `float` | 自发光强度因子 |
| `baseColor` | `float16_t4` | 基础颜色 (RGB) + 不透明度 (A) |
| `specular` | `float16_t4` | 高光参数（编码依赖着色模型） |
| `emissive` | `float3` | 自发光颜色 |
| `specularTransmission` | `float16_t` | 高光透射量 |
| `transmission` | `float16_t3` | 透射颜色 |
| `diffuseTransmission` | `float16_t` | 漫反射透射量 |
| `volumeScattering` | `float16_t3` | 体积散射系数 |
| `volumeAbsorption` | `float16_t3` | 体积吸收系数 |
| `volumeAnisotropy` | `float16_t` | 体积相函数各向异性 (g) |
| `displacementScale/Offset` | `float` | 位移缩放和偏移 |
| `texBaseColor` 等 | `TextureHandle` | 纹理句柄（共 6 个） |

### 位域访问器
| 方法 | 说明 |
|------|------|
| `setShadingModel/getShadingModel` | 着色模型（1 位） |
| `setNormalMapType/getNormalMapType` | 法线贴图类型（2 位） |
| `setDisplacementMinSamplerID/getDisplacementMinSamplerID` | 位移贴图最小值采样器 ID |
| `setDisplacementMaxSamplerID/getDisplacementMaxSamplerID` | 位移贴图最大值采样器 ID |

## 依赖关系
### import
- `TextureHandle.slang`, `MaterialData.slang`
