# VolumeProperties.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/VolumeProperties.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
定义了体积渲染属性结构 `VolumeProperties`，包含吸收系数、散射系数和相函数。

## 结构体与接口

### `VolumeProperties`
| 字段 | 类型 | 说明 |
|------|------|------|
| `sigmaA` | `float3` | 吸收系数 |
| `sigmaS` | `float3` | 散射系数 |
| `phaseFunction` | `IPhaseFunction` | 相函数（无效时为 NullPhaseFunction） |

## 依赖关系
### import
- `Rendering.Volumes.IPhaseFunction` - 相函数接口
