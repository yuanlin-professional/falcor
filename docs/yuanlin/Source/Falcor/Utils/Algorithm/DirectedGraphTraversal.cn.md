# DirectedGraphTraversal 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/DirectedGraphTraversal.h`
> 类型: C++ 头文件（纯头文件实现）
> 模块: Utils/Algorithm

## 功能概述

提供有向图的多种遍历算法，包括深度优先搜索（DFS）、广度优先搜索（BFS）、环路检测、拓扑排序和路径检测。所有算法基于 `DirectedGraph` 数据结构实现。通过模板参数化的容器类型（栈/队列）统一 DFS 和 BFS 的遍历逻辑。

## 类与接口

### `DirectedGraphTraversal`

- **继承**: 无（基类）
- **职责**: 图遍历算法的基类，管理遍历标志和访问记录

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `DirectedGraphTraversal(const DirectedGraph& graph, Flags flags)` | 构造函数，绑定图引用和遍历标志 |
| `bool reset(uint32_t rootNode)` | 重置遍历状态，从指定根节点开始 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGraph` | `const DirectedGraph&` | 待遍历的有向图引用 |
| `mFlags` | `Flags` | 遍历控制标志 |
| `mVisited` | `std::vector<bool>` | 节点访问记录（用于 IgnoreVisited 模式） |

#### `Flags` 枚举

| 值 | 说明 |
|----|------|
| `None` | 无特殊标志 |
| `Reverse` | 反向遍历（沿入边方向） |
| `IgnoreVisited` | 跳过已访问节点，避免重复遍历 |

### `DirectedGraphTraversalTemplate<Args>`

- **继承**: `DirectedGraphTraversal`
- **职责**: 模板化的图遍历实现，通过 Args 参数控制使用栈（DFS）或队列（BFS）

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `DirectedGraphTraversalTemplate(const DirectedGraph& graph, uint32_t rootNode, Flags flags)` | 构造并初始化遍历 |
| `uint32_t traverse()` | 遍历下一个节点，返回节点 ID；遍历完毕返回 `kInvalidID` |
| `bool reset(uint32_t rootNode)` | 重置遍历状态 |

### `DirectedGraphDfsTraversal`

- **职责**: 深度优先搜索遍历的类型别名（使用 `std::stack`）

### `DirectedGraphBfsTraversal`

- **职责**: 广度优先搜索遍历的类型别名（使用 `std::queue`）

### `DirectedGraphLoopDetector`

- **职责**: 检测有向图中从指定根节点出发是否存在环路

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static bool hasLoop(const DirectedGraph& graph, uint32_t rootNode)` | 使用 DFS 检测从根节点出发是否能回到自身 |

### `DirectedGraphTopologicalSort`

- **职责**: 对有向图进行拓扑排序

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::vector<uint32_t> sort(const DirectedGraph& graph)` | 返回拓扑排序后的节点 ID 序列 |

### `DirectedGraphPathDetector`（命名空间）

- **职责**: 检测有向图中两个节点之间是否存在路径

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool hasPath(const DirectedGraph& graph, uint32_t from, uint32_t to)` | 使用 DFS 检测从 from 到 to 是否存在路径 |
| `bool hasCycle(const DirectedGraph& graph, uint32_t root)` | 检测从根节点出发是否存在环（等价于 `hasPath(graph, root, root)`） |

## 依赖关系

### 本文件引用

- `Utils/Algorithm/DirectedGraph.h` — 有向图数据结构
- `Core/Macros.h` — 平台宏定义（`FALCOR_ENUM_CLASS_OPERATORS`）
- `Core/Error.h` — 断言宏

### 被以下文件引用

- Falcor 渲染图（RenderGraph）模块 — 用于依赖关系分析和执行顺序确定

## 实现细节

- **模板统一**: `DirectedGraphTraversalTemplate` 通过 `DfsArgs`/`BfsArgs` 策略类参数化容器类型和访问方式（`top()`/`front()`），避免 DFS 和 BFS 的代码重复。
- **反向遍历**: 设置 `Reverse` 标志后，遍历沿入边方向进行（获取父节点而非子节点），适用于依赖关系的反向追踪。
- **拓扑排序**: 使用经典的 DFS 后序遍历实现，遍历完所有后继节点后将当前节点压栈，最后逆序输出即为拓扑序。
- **环路检测**: `DirectedGraphLoopDetector::hasLoop` 不使用 `IgnoreVisited` 标志，因此可能存在重复访问；而 `DirectedGraphPathDetector` 使用了 `IgnoreVisited` 标志进行更高效的路径查找。
