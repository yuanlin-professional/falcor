# DirectedGraph 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/DirectedGraph.h`
> 类型: C++ 头文件（纯头文件实现）
> 模块: Utils/Algorithm

## 功能概述

实现了一个通用的有向图数据结构，支持节点和边的动态添加与删除。使用 `unordered_map` 存储节点和边，通过唯一的 ID 标识符进行索引。该图结构主要用于 Falcor 渲染框架内部的依赖关系管理，例如渲染图（Render Graph）的拓扑分析。

## 类与接口

### `DirectedGraph`

- **继承**: 无
- **职责**: 管理有向图的节点和边，提供增删查操作

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t addNode()` | 添加一个节点，返回唯一节点 ID |
| `std::unordered_set<uint32_t> removeNode(uint32_t id)` | 移除节点及其所有关联边，返回被移除的边 ID 集合 |
| `uint32_t addEdge(uint32_t srcNode, uint32_t dstNode)` | 添加从源节点到目标节点的有向边，返回唯一边 ID |
| `void removeEdge(uint32_t edgeId)` | 根据 ID 移除一条边 |
| `bool doesNodeExist(uint32_t nodeId) const` | 检查节点是否存在 |
| `bool doesEdgeExist(uint32_t edgeId) const` | 检查边是否存在 |
| `const Node* getNode(uint32_t nodeId) const` | 获取节点指针，不存在时返回 nullptr |
| `const Edge* getEdge(uint32_t edgeId) const` | 获取边指针，不存在时返回 nullptr |
| `uint32_t getCurrentNodeId() const` | 获取当前节点 ID 计数器值（等于已分配的最大 ID + 1） |
| `uint32_t getCurrentEdgeId() const` | 获取当前边 ID 计数器值 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mNodes` | `std::unordered_map<uint32_t, Node>` | 节点存储映射 |
| `mEdges` | `std::unordered_map<uint32_t, Edge>` | 边存储映射 |
| `mCurrentNodeId` | `uint32_t` | 节点 ID 自增计数器 |
| `mCurrentEdgeId` | `uint32_t` | 边 ID 自增计数器 |
| `kInvalidID` | `static constexpr uint32_t` | 无效 ID 常量（0xFFFFFFFF） |

### `DirectedGraph::Node`

- **职责**: 表示图中的一个节点，维护入边和出边列表

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t getOutgoingEdgeCount() const` | 获取出边数量 |
| `uint32_t getIncomingEdgeCount() const` | 获取入边数量 |
| `uint32_t getIncomingEdge(uint32_t i) const` | 获取第 i 条入边的 ID |
| `uint32_t getOutgoingEdge(uint32_t i) const` | 获取第 i 条出边的 ID |

### `DirectedGraph::Edge`

- **职责**: 表示图中的一条有向边，存储源节点和目标节点

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t getSourceNode() const` | 获取源节点 ID |
| `uint32_t getDestNode() const` | 获取目标节点 ID |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 错误处理宏（`FALCOR_UNREACHABLE`）
- `Utils/Logger.h` — 日志警告输出

### 被以下文件引用

- `Utils/Algorithm/DirectedGraphTraversal.h` — 图遍历算法
- Falcor 渲染图（RenderGraph）模块

## 实现细节

- **ID 分配策略**: 节点和边的 ID 采用单调递增分配，即使删除节点/边后 ID 也不会被回收。这简化了实现但在频繁增删场景下会浪费 ID 空间。
- **节点移除**: 移除节点时会自动找到并移除所有关联的入边和出边，使用模板函数 `findEdgesToRemove` 区分源/目标方向的查找。
- **边移除**: 从边的两端节点的入边/出边列表中分别删除该边的引用，使用模板函数 `removeEdgeFromNode` 区分入边和出边列表。
