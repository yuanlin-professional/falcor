# BCSDFConfig.slangh 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BCSDFConfig.slangh`
> 类型: Slang 着色器头文件
> 模块: Rendering/Materials

## 功能概述

定义 BCSDF（双向曲线散射分布函数）模型的静态编译期配置。用于选择毛发/毛皮材质的散射模型。默认值可通过宿主端传入的宏定义覆盖。

## 宏定义

| 宏名 | 值 | 说明 |
|------|-----|------|
| `DiffuseLambert` | 0 | Lambert 漫反射模型 |
| `HairChiang` | 1 | Chiang 2016 毛发模型 |
| `BCSDF` | `HairChiang`（默认） | 当前选用的 BCSDF 模型 |

## 依赖关系

无外部依赖。

## 实现细节

该文件计划在确定新的标准材质定义后移除。
