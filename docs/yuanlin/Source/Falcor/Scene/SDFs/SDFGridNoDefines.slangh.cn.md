# SDFGridNoDefines.slangh 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFGridNoDefines.slangh`
> 类型: Slang 头文件 (.slangh)
> 模块: Scene/SDFs

## 功能概述

提供 SDF 网格相关宏定义的默认值。当这些宏未在外部定义时,此文件提供安全的默认值以避免编译错误。

## 宏定义

| 宏定义 | 默认值 | 说明 |
|--------|--------|------|
| `SCENE_SDF_GRID_IMPLEMENTATION` | `0` | 当前使用的 SDF 网格实现类型 |
| `SCENE_SDF_GRID_IMPLEMENTATION_NDSDF` | `0xffffffff` | 归一化密集网格实现的标识值 |
| `SCENE_SDF_GRID_IMPLEMENTATION_SVS` | `0xffffffff` | 稀疏体素集实现的标识值 |
| `SCENE_SDF_GRID_IMPLEMENTATION_SBS` | `0xffffffff` | 稀疏砖块集实现的标识值 |
| `SCENE_SDF_GRID_IMPLEMENTATION_SVO` | `0xffffffff` | 稀疏体素八叉树实现的标识值 |

## 依赖关系

### 被以下文件引用
- `SDFGrid.slang`, `SDFGridHitData.slang` 以及其他需要 SDF 网格实现宏定义的着色器文件
