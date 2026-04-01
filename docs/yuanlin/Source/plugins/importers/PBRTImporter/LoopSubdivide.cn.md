# LoopSubdivide 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/LoopSubdivide.h`, `LoopSubdivide.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/PBRTImporter

## 功能概述

实现 Loop 细分曲面算法。将三角形网格进行指定层级的细分，生成细化后的顶点位置、法线和索引。基于 pbrt 的实现（Apache-2.0 许可）。

## 类与接口

### `LoopSubdivideResult`

- **职责**: 存储 Loop 细分的输出结果。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `positions` | `vector<float3>` | 细分后的顶点位置 |
| `normals` | `vector<float3>` | 细分后的顶点法线 |
| `indices` | `vector<uint32_t>` | 三角形索引 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `LoopSubdivideResult loopSubdivide(uint32_t levels, span<const float3> positions, span<const uint32_t> vertices)` | 执行指定层数的 Loop 细分 |

## 依赖关系

### 本文件引用

- `Utils/Math/Vector.h` - `float3`
- `fstd/span.h` - 非拥有数组视图

### 被以下文件引用

- `PBRTImporter.cpp` - 处理 `loopsubdiv` 形状类型
