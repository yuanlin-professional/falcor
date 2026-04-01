# NumericRange.h 源码文档

> 路径: `Source/Falcor/Utils/NumericRange.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供可迭代的数值范围类，类似于 Python 的 `range()`。可用于基于范围的 for 循环中遍历整数区间。设计为 C++20 `std::views::iota` 可用前的替代方案。

## 类与接口

### `NumericRange<T>`

- **继承**: 无
- **模板约束**: `T` 必须为整数类型（`std::is_integral`）
- **职责**: 表示一个 `[begin, end)` 的整数范围，支持前向迭代

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `NumericRange(const T& begin, const T& end)` | 构造范围，要求 begin <= end |
| `Iterator begin() const` | 返回起始迭代器 |
| `Iterator end() const` | 返回结束迭代器 |

### `NumericRange<T>::Iterator`

- **类别**: 前向迭代器
- **职责**: 遍历数值范围

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mBegin` | `T` | 范围起始值 |
| `mEnd` | `T` | 范围结束值（不包含） |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 范围检查

### 被以下文件引用

- 需要整数范围迭代的模块

## 实现细节

- 不可拷贝、不可移动、不可默认构造
- 迭代器仅支持前向遍历（`++` 和 `!=`）
- 主模板为空类，仅整数类型的特化有实现
