# InverseOptimizationParams.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/InverseOptimizationParams.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件声明了逆渲染优化参数的全局 ParameterBlock。它导出 `SharedTypes` 中定义的 `InverseOptimizationParams` 结构体，并实例化为全局着色器变量 `gInvOpt`，用于指定当前优化迭代中需要计算梯度的目标网格 ID。

## 结构体与接口

本文件不定义新的结构体。`InverseOptimizationParams` 结构体定义在 `SharedTypes.slang` 中，包含：

| 成员 | 类型 | 说明 |
|------|------|------|
| `meshID` | `uint32_t` | 当前需要计算梯度的目标网格 ID |
| `_pad0` | `uint3` | 对齐填充 |

## 全局变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gInvOpt` | `ParameterBlock<InverseOptimizationParams>` | 逆优化参数块 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `DiffRendering.SharedTypes` | 导出共享类型定义（`__exported import`） |

### 被以下文件引用

- `DiffRendering/DiffSceneIO.slang` — 在反向传播中检查 `gInvOpt.meshID` 判断是否计算梯度
