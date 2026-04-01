# DiffDebugParams.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/DiffDebugParams.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件声明了可微渲染调试参数的全局 ParameterBlock。它导出 `SharedTypes` 中定义的 `DiffDebugParams` 结构体，并实例化为全局着色器变量 `gDiffDebug`，供其他可微渲染着色器在前向/反向模式调试时访问调试参数。

## 结构体与接口

本文件不定义新的结构体。`DiffDebugParams` 结构体定义在 `SharedTypes.slang` 中，包含：

| 成员 | 类型 | 说明 |
|------|------|------|
| `varType` | `DiffVariableType` | 调试的可微变量类型（材质/几何平移等） |
| `id` | `uint2` | 目标标识（如实例 ID、材质 ID 等） |
| `offset` | `uint32_t` | 参数偏移量 |
| `grad` | `float4` | 调试用梯度值 |

## 全局变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gDiffDebug` | `ParameterBlock<DiffDebugParams>` | 可微渲染调试参数块 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `DiffRendering.SharedTypes` | 导出共享类型定义（`__exported import`） |

### 被以下文件引用

- `DiffRendering/DiffSceneIO.slang`
- `DiffRendering/GradientIOWrapper.slang`
