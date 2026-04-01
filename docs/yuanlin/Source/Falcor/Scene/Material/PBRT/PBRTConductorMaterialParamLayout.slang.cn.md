# PBRTConductorMaterialParamLayout.slang 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTConductorMaterialParamLayout.slang`
> 类型: Slang 着色器文件（参数布局定义）
> 模块: Scene/Material/PBRT

## 功能概述

定义 PBRT 导体材质的参数布局，用于 CPU/GPU 之间的材质参数序列化与反序列化。通过宏系统声明参数的类型、偏移、访问器函数名称和显示名称。

## 结构体与接口

### 常量定义

| 名称 | 类型 | 值 | 说明 |
|------|------|----|------|
| `kPBRTConductorMinRoughness` | `float2` | `(0.05, 0.05)` | 导体材质的最小粗糙度限制 |

### 参数布局 (`MATERIAL_PARAMS`)

| 参数类型 | 偏移 | 名称 | Getter | Setter | 显示名 | 备注 |
|----------|------|------|--------|--------|--------|------|
| `float3` | 0 | `baseColor` | `getBaseColor3` | `setBaseColor3` | "eta" | 复数折射率实部 |
| `float3` | 3 | `transmissionColor` | `getTransmissionColor` | `setTransmissionColor` | "k" | 消光系数 |
| `float2` | 6 | `roughness` | `getRoughness` | `setRoughness` | "roughness" | 受最小粗糙度限制 |

## 函数

通过 `DEFINE_MATERIAL_PARAM_LAYOUT(PBRTConductorMaterial, MATERIAL_PARAMS)` 宏自动生成：
- `PBRTConductorMaterialParamLayout::layout()` — 返回参数布局描述
- `PBRTConductorMaterialParamLayout::serialize()` — 将材质参数序列化
- `PBRTConductorMaterialParamLayout::deserialize()` — 反序列化材质参数

## 依赖关系

### import

- `../MaterialParamLayout.slangh` — 参数布局宏定义框架
