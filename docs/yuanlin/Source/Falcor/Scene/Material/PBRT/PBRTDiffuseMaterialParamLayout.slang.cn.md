# PBRTDiffuseMaterialParamLayout.slang 源码文档

> 路径: `Source/Falcor/Scene/Material/PBRT/PBRTDiffuseMaterialParamLayout.slang`
> 类型: Slang 着色器文件（参数布局定义）
> 模块: Scene/Material/PBRT

## 功能概述

定义 PBRT 漫反射材质的参数布局，用于 CPU/GPU 之间的材质参数序列化与反序列化。

## 结构体与接口

### 参数布局 (`MATERIAL_PARAMS`)

| 参数类型 | 偏移 | 名称 | Getter | Setter | 显示名 |
|----------|------|------|--------|--------|--------|
| `float3` | 0 | `baseColor` | `getBaseColor3` | `setBaseColor3` | "diffuse" |

## 函数

通过 `DEFINE_MATERIAL_PARAM_LAYOUT(PBRTDiffuseMaterial, MATERIAL_PARAMS)` 宏自动生成：
- `PBRTDiffuseMaterialParamLayout::layout()` — 返回参数布局描述
- `PBRTDiffuseMaterialParamLayout::serialize()` — 序列化
- `PBRTDiffuseMaterialParamLayout::deserialize()` — 反序列化

## 依赖关系

### import

- `../MaterialParamLayout.slangh` — 参数布局宏定义框架
