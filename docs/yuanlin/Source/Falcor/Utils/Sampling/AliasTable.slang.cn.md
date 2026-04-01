# AliasTable.slang 源码文档

> 路径: `Source/Falcor/Utils/Sampling/AliasTable.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Sampling

## 功能概述

实现了 GPU 端的 **别名表（Alias Table）** 采样结构体，用于从离散概率分布中进行 O(1) 时间复杂度的随机采样。此文件是 C++ 端 `AliasTable` 类的着色器对应部分，通过结构化缓冲区读取由 CPU 端构建并上传的别名表数据。

## 结构体与接口

### `AliasTable`

主要的别名表采样结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `items` | `StructuredBuffer<Item>` | 别名表条目列表 |
| `weights` | `StructuredBuffer<float>` | 原始权重列表 |
| `count` | `uint` | 权重总数 |
| `weightSum` | `float` | 所有权重之和 |

### `AliasTable::Item`

别名表的单个条目。

| 成员 | 类型 | 说明 |
|------|------|------|
| `threshold` | `uint` | 阈值（以 uint 存储，通过 `asfloat` 转换为 float） |
| `indexA` | `uint` | 重定向索引 A |
| `indexB` | `uint` | 原始索引 B |
| `_pad` | `uint` | 内存对齐填充 |

## 函数

### `AliasTable` 方法

| 函数签名 | 说明 |
|----------|------|
| `uint sample(uint index, float rnd)` | 给定均匀随机索引 [0, count) 和随机数 [0, 1)，返回按权重比例采样的条目索引。若 rnd >= threshold 则返回 indexA，否则返回 indexB |
| `uint sample(float2 rnd)` | 使用两个随机数进行采样：第一个用于选择别名表条目，第二个用于在该条目内选择 |
| `float getWeight(uint index)` | 获取指定索引处的原始权重 |

### `Item` 方法

| 函数签名 | 说明 |
|----------|------|
| `float getThreshold()` | 将 uint 类型的 threshold 转换为 float 并返回 |
| `uint getIndexA()` | 返回索引 A |
| `uint getIndexB()` | 返回索引 B |

## 依赖关系 / import

- 无外部 import（独立的着色器结构体定义）

## 实现细节

- `threshold` 以 `uint` 类型存储，使用 `asfloat()` 进行类型转换，这是为了在结构化缓冲区中保持位精确的数据布局
- `sample(float2 rnd)` 重载将第一个随机数映射为 [0, count) 范围的索引，第二个随机数用于阈值比较
- 与 C++ 端的 `AliasTable` 类配合使用：CPU 端构建表并上传到 GPU 缓冲区，着色器端通过此结构体进行采样
