# UsdIndexedVector 源码文档

> 路径: `Source/Modules/USDUtils/Tessellator/UsdIndexedVector.h`
> 类型: C++ 头文件（仅头文件）
> 模块: USDUtils / Tessellator

## 功能概述

模板容器类，将可能包含重复元素的向量转换为唯一数据集加索引列表的形式。用于网格细分过程中的顶点去重和 UV 索引化。

## 类与接口

### `UsdIndexedVector<T, I, H, E>`

- **模板参数**:
  - `T`: 数据元素类型
  - `I`: 索引类型
  - `H`: 哈希函数对象类型
  - `E`: 相等比较函数对象类型（默认 `std::equal_to<T>`）
- **职责**: 维护唯一值集合和按添加顺序的索引列表。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void append(const T& v)` | 添加元素，自动去重 |
| `bool append(const T& v, uint32_t& outIdx)` | 添加元素，返回唯一索引和是否为新插入 |
| `const VtArray<T>& getValues() const` | 获取唯一数据项集合 |
| `const VtArray<I>& getIndices() const` | 获取有序索引列表 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mIndexMap` | `unordered_map<T, I, H, E>` | 值到索引的映射 |
| `mValues` | `VtArray<T>` | 唯一值数组 |
| `mIndices` | `VtArray<I>` | 按添加顺序的索引数组 |

## 依赖关系

### 本文件引用

- `pxr/base/vt/array.h` - USD 的 `VtArray` 容器

### 被以下文件引用

- `Tessellation.cpp` - 用于顶点位置去重和 UV 索引化
