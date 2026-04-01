# UnionFind 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/UnionFind.h`
> 类型: C++ 头文件（纯头文件实现）
> 模块: Utils/Algorithm

## 功能概述

实现了经典的并查集（Union-Find / Disjoint Set Union）数据结构，支持任意无符号整数类型作为元素标识。通过路径压缩（path compression）和按大小合并（union by size）两种优化策略，使得查找和合并操作接近 O(1) 的均摊时间复杂度。

## 类与接口

### `UnionFind<T>`

- **继承**: 无
- **职责**: 管理不相交集合，支持高效的集合查找与合并操作
- **模板约束**: `T` 必须为无符号整数类型（通过 `static_assert` 检查 `std::is_unsigned_v<T>`）

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `UnionFind()` | 默认构造函数 |
| `UnionFind(size_t size)` | 构造并初始化指定大小的并查集，每个元素初始为独立集合 |
| `void reset(size_t size)` | 重置并查集为指定大小，每个元素初始化为自身的父节点 |
| `T findSet(T v)` | 查找元素 v 所属集合的代表元素（根节点），使用路径压缩优化 |
| `bool connectedSets(T v0, T v1)` | 判断两个元素是否属于同一集合 |
| `void unionSet(T v0, T v1)` | 合并两个元素所在的集合，使用按大小合并策略（小集合挂在大集合下） |
| `size_t getSetCount() const` | 获取当前不相交集合的数量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mParent` | `std::vector<T>` | 父节点数组，`mParent[i]` 存储元素 i 的父节点 |
| `mSetSize` | `std::vector<size_t>` | 集合大小数组，仅根节点处的值有意义 |
| `mSetCount` | `size_t` | 当前不相交集合的总数 |

## 依赖关系

### 本文件引用

- `<vector>` — 标准库动态数组
- `<cstddef>` — `size_t` 类型定义

### 被以下文件引用

- 需要进行连通分量分析或等价类合并的模块

## 实现细节

- **路径压缩**: `findSet` 使用递归路径压缩，将查找路径上的所有节点直接连接到根节点，缩短后续查找的链长度
- **按大小合并**: `unionSet` 总是将较小的集合挂在较大集合的根节点下，保持树的平衡性
- **集合计数**: 每次成功合并（两个不同集合）时 `mSetCount` 减一，初始值等于元素总数
- **类型安全**: 使用 `static_assert` 确保模板类型为无符号整数，避免负数索引问题
