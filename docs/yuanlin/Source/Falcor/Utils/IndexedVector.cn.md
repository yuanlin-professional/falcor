# IndexedVector.h 源码文档

> 路径: `Source/Falcor/Utils/IndexedVector.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供将可能包含重复项的向量转换为索引向量的工具类。通过哈希去重，将输入数据分解为唯一值集合和对应的索引列表，减少内存占用并加速查找。

## 类与接口

### `IndexedVector<T, I, H, E>`

- **继承**: 无
- **模板参数**:
  - `T` — 底层数据类型
  - `I` — 索引值类型
  - `H` — 哈希对象类型
  - `E` — 相等比较对象类型（默认 `std::equal_to<T>`）
- **职责**: 维护唯一值集合和有序索引列表

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void append(const T& v)` | 追加数据项 |
| `bool append(const T& v, uint32_t& outIdx)` | 追加数据项，输出唯一项索引，返回是否为新插入 |
| `fstd::span<const T> getValues() const` | 获取唯一值集合 |
| `fstd::span<const I> getIndices() const` | 获取有序索引列表 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mIndexMap` | `std::unordered_map<T, I, H, E>` | 值到索引的映射 |
| `mValues` | `std::vector<T>` | 唯一值集合 |
| `mIndices` | `std::vector<I>` | 有序索引列表 |

## 依赖关系

### 本文件引用

- `Utils/fast_vector.h` — 快速向量（头文件引用但未直接使用）
- `<unordered_map>`、`<vector>`、`<fstd/span.h>`

### 被以下文件引用

- 网格导入、顶点去重等需要索引化数据的模块

## 实现细节

- 每次 `append` 都会将索引追加到 `mIndices`，无论该值是否为重复项
- 使用自定义哈希和相等比较，适应不同数据类型的去重需求
