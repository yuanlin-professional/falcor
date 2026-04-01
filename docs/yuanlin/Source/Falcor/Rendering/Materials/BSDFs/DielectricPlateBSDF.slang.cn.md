# DielectricPlateBSDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/DielectricPlateBSDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现薄（建筑学）电介质板双向散射分布函数，用于在单个多边形上模拟平行平面材质。反射使用经典微表面理论，透射粗糙度根据反射粗糙度和 IOR 计算。Fresnel 项使用标准玻璃理论（含多次内部反射近似）。

## 结构体与接口

### `DielectricPlateBSDF`

- **实现**: `IBSDF`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `D` | `AnisotropicGGX` | 反射粗糙度 |
| `DInside` | `AnisotropicGGX` | 透射粗糙度 |
| `eta` | `float` | 折射率 |

#### 叶瓣类型
- 光滑时: `DeltaReflection` + `DeltaTransmission`
- 粗糙时: `SpecularReflection` + `SpecularTransmission`

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.Fresnel`
- `Rendering.Materials.AnisotropicGGX`
