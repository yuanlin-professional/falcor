# DiffMaterialData.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/DiffMaterialData.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件定义了用于 BSDF 评估的可微材质数据结构 `DiffMaterialData`。该结构体实现了 `IDifferentiable` 接口，存储着色帧（ShadingFrame）和材质参数数组，并提供可微的读写方法，使得材质参数在可微渲染的反向传播中能够正确传递梯度。

## 结构体与接口

### `DiffMaterialData`

- **实现接口**: `IDifferentiable`
- **职责**: 存储可微的 BSDF 材质参数，支持通过偏移量顺序读写参数

#### 关键常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kMaterialParamCount` | `20` | 材质参数数组的最大长度 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sf` | `ShadingFrame` | 着色坐标系 |
| `data` | `float[kMaterialParamCount]` | 材质参数数组 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `[Differentiable] __init()` | 可微构造函数，初始化着色帧为单位帧，参数全部置零 |
| `[Differentiable] float read(inout uint offset)` | 读取单个浮点参数并自增偏移量 |
| `[Differentiable] void read<N>(out vector<float,N> value, inout uint offset)` | 读取 N 维浮点向量参数 |
| `[Differentiable] vector<float,N> read<N>(inout uint offset)` | 读取 N 维浮点向量参数（返回值形式） |
| `[mutating][Differentiable] void write(float value, inout uint offset)` | 写入单个浮点参数 |
| `[mutating][Differentiable] void write<N>(vector<float,N> value, inout uint offset)` | 写入 N 维浮点向量参数 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Scene.Material.ShadingUtils` | 着色工具，提供 `ShadingFrame` 等类型 |
