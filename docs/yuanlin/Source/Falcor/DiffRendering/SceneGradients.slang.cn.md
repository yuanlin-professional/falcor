# SceneGradients.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/SceneGradients.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件定义了着色器端的场景梯度管理结构体 `SceneGradients`，作为 GPU 上梯度缓冲区的访问接口。它管理各梯度类型对应的临时缓冲区，提供梯度读取和原子累加写入方法，是可微渲染着色器中梯度存取的核心组件。全局变量 `gSceneGradients` 作为 ParameterBlock 供所有可微渲染着色器共享。

## 结构体与接口

### `SceneGradients`

- **职责**: 着色器端场景梯度缓冲区的管理和访问接口

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `gradDim[]` | `uint[GradientType::Count]` | 各梯度类型的维度 |
| `hashSize[]` | `uint[GradientType::Count]` | 各梯度类型的哈希表大小 |
| `tmpGrads[]` | `RWByteAddressBuffer[GradientType::Count]` | 各梯度类型的临时缓冲区 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint getGradDim(GradientType)` | 获取指定梯度类型的维度 |
| `uint getHashSize(GradientType)` | 获取指定梯度类型的哈希表大小 |
| `float getGrad(GradientType, uint gradIndex, uint hashIndex)` | 读取指定位置的梯度值 |
| `void atomicAddGrad(GradientType, uint gradIndex, uint hashIndex, float value)` | 原子累加梯度值到临时缓冲区 |

## 全局变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gSceneGradients` | `ParameterBlock<SceneGradients>` | 全局场景梯度参数块 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Utils/NVAPI.slangh` | NVIDIA API 扩展（用于 `InterlockedAddF32`） |
| `DiffRendering.SharedTypes` | 导出共享类型定义（`__exported import`） |

### 被以下文件引用

- `DiffRendering/GradientIOWrapper.slang`
- `DiffRendering/DiffSceneIO.slang`
- `DiffRendering/SceneGradients.cpp`（C++ 端通过此文件创建参数块）

## 实现细节

- `atomicAddGrad` 在写入前检查 `gradIndex < gradDim` 以防越界
- 使用 `InterlockedAddF32`（NVAPI 扩展）实现浮点原子加操作
- 缓冲区布局为 `hashIndex * gradDim + gradIndex`，每个元素 4 字节
- `#ifdef SCENE_GRADIENTS_BLOCK` 条件编译用于 C++ 端创建参数块时的空入口点
