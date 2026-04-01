# MaterialTypes.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/MaterialTypes.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: Scene/Material

## 功能概述
定义了材质系统使用的核心枚举类型：材质类型、Alpha 模式、着色模型和法线贴图类型。

## 结构体与接口

### `MaterialType` 枚举
| 值 | 说明 |
|----|------|
| `Unknown` | 未知材质类型 |
| `Standard` | 标准 PBR 材质 |
| `Cloth` | 布料材质 |
| `Hair` | 头发材质 |
| `MERL` | MERL 测量材质 |
| `MERLMix` | MERL 混合材质 |
| `PBRTDiffuse` | PBRT 漫反射材质 |
| `PBRTDiffuseTransmission` | PBRT 漫反射透射材质 |
| `PBRTConductor` | PBRT 导体材质 |
| `PBRTDielectric` | PBRT 电介质材质 |
| `PBRTCoatedConductor` | PBRT 涂层导体材质 |
| `PBRTCoatedDiffuse` | PBRT 涂层漫反射材质 |
| `RGL` | RGL 测量材质 |
| `BuiltinCount` | 内置类型总数（必须在最后） |

### `AlphaMode` 枚举
| 值 | 说明 |
|----|------|
| `Opaque` | 不透明（无 Alpha 测试） |
| `Mask` | Alpha 遮罩 |

### `ShadingModel` 枚举
| 值 | 说明 |
|----|------|
| `MetalRough` | 金属度/粗糙度模型 |
| `SpecGloss` | 高光/光泽度模型 |

### `NormalMapType` 枚举
| 值 | 说明 |
|----|------|
| `None` | 不使用法线贴图 |
| `RGB` | RGB 通道编码法线 |
| `RG` | 切线空间 RG 通道编码 |

## 依赖关系
### import
- `Utils/HostDeviceShared.slangh`
