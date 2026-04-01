# GradientIOWrapper.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/GradientIOWrapper.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件实现了梯度 I/O 包装器 `GradientIOWrapper`，用于在可微渲染的反向传播过程中设置梯度数据流。其核心设计是通过自定义导数函数（`ForwardDerivative` / `BackwardDerivative`），在前向传播时直接传递原始值，在反向传播时将梯度通过原子操作写入场景梯度缓冲区的指定偏移位置。这是材质参数梯度回传的关键桥梁。

## 结构体与接口

### `GradientIOWrapper`

- **职责**: 包装材质参数的可微读取，在反向传播时将梯度路由到正确的梯度缓冲区位置

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `gradType` | `GradientType` | 梯度类型（材质、网格位置等） |
| `baseOffset` | `uint` | 梯度缓冲区中的基础偏移量 |
| `hashIndex` | `uint` | 哈希索引，用于避免写入冲突 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `float getFloat(float val, uint offset)` | 获取单个浮点值（前向返回原值，反向写入梯度） |
| `fwd_d_getFloat(...)` | `getFloat` 的前向导数：返回零导数 |
| `bwd_d_getFloat(...)` | `getFloat` 的反向导数：将梯度原子累加到缓冲区 |
| `vector<float,N> getFloat<N>(vector<float,N> val, uint offset)` | 获取 N 维浮点向量（泛型版本） |
| `fwd_d_getFloats<N>(...)` | 向量版本的前向导数，前向调试模式下可注入调试梯度 |
| `bwd_d_getFloats<N>(...)` | 向量版本的反向导数，逐分量原子累加梯度 |
| `[Differentiable] float getFloat(float, MaterialParamLayoutEntry)` | 使用材质参数布局的便捷包装 |
| `[Differentiable] vector<float,N> getFloat<N>(vector<float,N>, MaterialParamLayoutEntry)` | 向量版本的材质参数布局便捷包装 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint hashFunction(uint index, uint hashSize)` | 使用 Jenkins 哈希计算哈希索引 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `DiffRendering.SceneGradients` | 场景梯度缓冲区（`gSceneGradients`） |
| `DiffRendering.DiffDebugParams` | 调试参数（`gDiffDebug`） |
| `Scene.Material.MaterialParamLayout` | 材质参数布局定义 |
| `Utils.Math.HashUtils` | 哈希工具函数（`jenkinsHash`） |

### 被以下文件引用

- `DiffRendering/DiffSceneIO.slang` — 用于场景梯度的 I/O 操作
