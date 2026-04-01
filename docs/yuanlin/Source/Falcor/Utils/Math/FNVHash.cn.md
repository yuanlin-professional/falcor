# FNVHash 源码文档

> 路径: `Source/Falcor/Utils/Math/FNVHash.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

实现 Fowler-Noll-Vo (FNV) 哈希算法，支持 32 位和 64 位版本。FNV 是一种高效的非加密哈希函数，适用于哈希表、数据去重等场景。

## 类与接口
### `FNVHash<TUInt>`
- **继承**: 无
- **职责**: 累积式 FNV 哈希计算器，可以逐步插入数据进行哈希

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `void insert(const void* begin, const void* end)` | 插入 [begin, end) 范围内的数据 |
| `void insert(const void* data, size_t size)` | 插入指定大小的数据 |
| `void insert(const T& data)` | 插入单个数据对象 |
| `TUInt get() const` | 获取当前哈希值 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mHash` | `TUInt` | 当前哈希状态，初始化为 FNV offset basis |

### 类型别名
| 别名 | 说明 |
|------|------|
| `FNVHash64` | 64 位 FNV 哈希 |
| `FNVHash32` | 32 位 FNV 哈希 |

### 辅助函数
| 函数签名 | 说明 |
|----------|------|
| `uint64_t fnvHashArray64(const void* data, size_t size)` | 一次性计算 64 位哈希 |
| `uint32_t fnvHashArray32(const void* data, size_t size)` | 一次性计算 32 位哈希 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`
- `Core/Error.h`
