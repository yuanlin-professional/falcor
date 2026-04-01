# PackedTypes.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/PackedTypes.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/RTXDI

## 功能概述

本文件定义了 RTXDI 模块中用于 GPU 缓冲区存储的紧凑打包数据类型。这些类型是对应高级结构体（`PolymorphicLight`、`SurfaceData`）的紧凑二进制表示，用于在结构化缓冲区中高效传输和存储。

## 结构体与接口

### `PackedPolymorphicLight`

多态光源的打包表示，包含光源类型、位置、边向量或光源 ID 等信息。

| 成员 | 类型 | 说明 |
|------|------|------|
| `header` | `uint` | 头部信息（高 2 位为类型，低 30 位为附加数据） |
| `data0` | `uint3` | 数据域 0（如世界坐标位置） |
| `data1` | `uint4` | 数据域 1（如边向量、辐射度等） |

### `PackedSurfaceData`

表面数据的打包表示，存储主命中点的几何和材质信息。

| 成员 | 类型 | 说明 |
|------|------|------|
| `position` | `uint3` | 世界空间位置（float 重新解释为 uint） |
| `viewDepth` | `uint` | 观察深度（float 重新解释为 uint） |
| `normal` | `uint` | 打包法线（八面体映射 2x16 位） |
| `weights` | `uint` | 打包权重（漫反射/高光反射/粗糙度/漫反射概率各 8 位 UNORM） |
| `_pad` | `uint2` | 填充对齐 |

## 依赖关系 / import

- 无外部依赖（自包含）

## 实现细节

- `PackedPolymorphicLight` 总大小 32 字节（8 个 uint），所有光源类型统一为此布局
- `PackedSurfaceData` 总大小 32 字节（8 个 uint），使用位打包技术将多个标量压缩到单个 uint 中
- 这些类型通过 `__exported import` 被 `PolymorphicLight.slang` 和 `SurfaceData.slang` 引用
