# BSDFConfig.slangh 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFConfig.slangh`
> 类型: Slang 着色器头文件
> 模块: Rendering/Materials

## 功能概述

定义双向散射分布函数（BSDF）模型的静态编译期配置，包括漫反射 BRDF 类型和镜面遮蔽函数类型。默认值可通过宿主端传入的宏定义覆盖。

## 宏定义

### 漫反射 BRDF 选择
| 宏名 | 值 | 说明 |
|------|-----|------|
| `DiffuseBrdfLambert` | 0 | Lambert 漫反射 |
| `DiffuseBrdfDisney` | 1 | Disney 漫反射 |
| `DiffuseBrdfFrostbite` | 2 | Frostbite 漫反射（默认） |
| `DiffuseBrdf` | `DiffuseBrdfFrostbite` | 当前选用的漫反射模型 |

### 镜面遮蔽函数选择
| 宏名 | 值 | 说明 |
|------|-----|------|
| `SpecularMaskingFunctionSmithGGXSeparable` | 0 | UE4 使用的可分离形式 |
| `SpecularMaskingFunctionSmithGGXCorrelated` | 1 | Frostbite 使用的相关形式（默认，更精确） |
| `SpecularMaskingFunction` | `...Correlated` | 当前选用的遮蔽函数 |

## 依赖关系

无外部依赖。
