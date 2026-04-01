# fast_vector.h 源码文档

> 路径: `Source/Falcor/Utils/fast_vector.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供 `std::vector` 的高性能替代实现。在 MSVC 的 Debug 模式下，STL 的 `vector::push_back` 存在全局锁，导致多线程环境中性能显著下降。`fast_vector` 通过简单的内存管理避免了这一问题。

## 类与接口

### `fast_vector<T>`

- **继承**: 无
- **约束**: `T` 必须是平凡类型（`std::is_trivial_v<T>`）
- **职责**: 作为 `std::vector` 的替代品，用于存储平凡类型的动态数组，在 Debug 多线程场景下性能更优

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void reserve(size_t capacity)` | 预留容量 |
| `void resize(size_t size)` | 调整大小 |
| `void resize(size_t capacity, const T& value)` | 调整大小并填充值 |
| `void push_back(const T& v)` | 追加元素 |
| `void assign(FwdIterator b, FwdIterator e)` | 从迭代器范围赋值 |
| `operator std::vector<T>()` | 隐式转换为 `std::vector` |
| `size_t size() const` | 元素数量 |
| `bool empty() const` | 是否为空 |
| `void clear()` | 清空（仅重置大小，不释放内存） |
| `T* data()` | 底层数据指针 |
| `T& operator[](size_t pos)` | 按索引访问 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `m_capacity` | `size_t` | 当前容量 |
| `m_size` | `size_t` | 当前元素数量 |
| `m_data` | `std::unique_ptr<T[]>` | 底层数组 |

## 依赖关系

### 本文件引用

- `<vector>`、`<memory>`、`<type_traits>`

### 被以下文件引用

- `IndexedVector.h` 等需要高性能动态数组的模块

## 实现细节

- 增长策略为容量翻倍（`max(capacity*2, required_size)`）
- 使用 `memcpy` 进行数据迁移（因此仅支持平凡类型）
- `clear()` 仅将 `m_size` 置零，不释放内存
- 支持从 `std::vector` 构造和赋值
