# StateGraph 源码文档

> 路径: `Source/Falcor/Core/State/StateGraph.h`
> 类型: C++ 头文件（模板类，仅头文件实现）
> 模块: Core/State

## 功能概述

`StateGraph` 是一个通用的状态缓存图模板类，用于高效管理和查找管线状态对象。它以有向图的形式组织状态节点，节点之间通过带标签的边连接。每个节点存储一个管线状态对象（如 `ComputeStateObject` 或 `GraphicsStateObject`），边代表状态变化（如着色器程序切换、FBO 变化等）。

该类是 `ComputeState` 和 `GraphicsState` 的核心缓存基础设施，通过避免重复创建管线状态对象来显著提升渲染性能。

## 类与接口

### `StateGraph<NodeType, EdgeType, EdgeHashType>`

- **继承**: 无
- **职责**: 维护一个有向状态图，支持沿边导航、节点数据设置以及全局匹配搜索。

#### 模板参数

| 参数 | 说明 |
|------|------|
| `NodeType` | 节点数据类型，通常为 `ref<ComputeStateObject>` 或 `ref<GraphicsStateObject>` |
| `EdgeType` | 边标签类型，通常为 `void*`（使用指针值作为状态变化标识） |
| `EdgeHashType` | 边标签的哈希函数类型，默认为 `std::hash<EdgeType>` |

#### 类型别名

| 别名 | 类型 | 说明 |
|------|------|------|
| `CompareFunc` | `std::function<bool(const NodeType& data)>` | 节点匹配比较函数 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `StateGraph()` | 构造函数，初始化图为包含一个空节点的状态，当前节点索引为 0。 |
| `bool isEdgeExists(const EdgeType& e) const` | 检查当前节点是否存在指定标签的出边。 |
| `bool walk(const EdgeType& e)` | 沿指定边从当前节点前进。若边存在，移动到目标节点并返回 `true`；若边不存在，创建新节点和新边并移动到新节点，返回 `false`。 |
| `const NodeType& getCurrentNode() const` | 获取当前节点存储的数据（管线状态对象）。 |
| `void setCurrentNodeData(const NodeType& data)` | 设置当前节点的数据。通常在创建新的管线状态对象后调用。 |
| `bool scanForMatchingNode(CompareFunc cmpFunc)` | 遍历整个图中除当前节点外的所有节点，使用比较函数查找匹配节点。若找到，将所有指向当前节点的边重定向到匹配节点，并将当前节点切换到匹配节点。返回是否找到匹配。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGraph` | `std::vector<Node>` | 节点数组，存储整个状态图 |
| `mCurrentNode` | `uint32_t` | 当前节点索引，初始为 0 |

### 内部结构体 `Node`

| 成员 | 类型 | 说明 |
|------|------|------|
| `data` | `NodeType` | 节点存储的数据（管线状态对象），默认初始化为 `{0}` |
| `edges` | `edge_map` | 出边映射表，类型为 `std::unordered_map<EdgeType, uint32_t, EdgeHashType>`，键为边标签，值为目标节点索引 |

## 依赖关系

### 本文件引用

| 头文件 | 说明 |
|--------|------|
| `<functional>` | 标准库，用于 `std::function` |
| `<vector>` | 标准库，用于节点数组 |
| `<unordered_map>` | 标准库，用于边映射表 |
| `<cstdint>` | 标准库，用于 `uint32_t` |

### 被以下文件引用

| 文件 | 说明 |
|------|------|
| `Core/State/ComputeState.h` | 使用 `StateGraph<ref<ComputeStateObject>, void*>` 缓存计算管线状态 |
| `Core/State/GraphicsState.h` | 使用 `StateGraph<ref<GraphicsStateObject>, void*>` 缓存图形管线状态 |
| `Core/Program/Program.h` | 程序类中也引用了状态图 |

## 实现细节

### 图结构

状态图使用邻接表表示，节点存储在 `std::vector<Node>` 中，通过整数索引引用。每个节点包含一个数据域和一个出边映射表（`unordered_map`）。图初始化时包含一个空的根节点（索引 0）。

### walk 导航机制

`walk()` 是状态图的核心操作。调用者传入一个边标签（通常是某个状态组件的指针值），表示状态发生了变化：

- **边已存在**: 直接沿边移动到目标节点，返回 `true`。该节点可能已缓存了对应的管线状态对象。
- **边不存在**: 创建新节点并建立新边，移动到新节点，返回 `false`。新节点的数据为空，调用者需要后续设置。

### scanForMatchingNode 匹配搜索

当 `walk()` 导航到一个空节点（尚未缓存管线状态对象）时，`scanForMatchingNode()` 会遍历整个图寻找已有的匹配节点。若找到匹配：

1. 遍历所有节点的所有边，将指向当前（空）节点的边重定向到匹配节点。
2. 将当前节点切换为匹配节点。

这种重定向机制确保后续相同路径的导航可以直接到达正确的缓存节点，避免重复搜索。

### 使用模式

在 `ComputeState` 和 `GraphicsState` 中，状态图的典型使用流程如下：

1. 每当某个状态组件（程序内核、FBO、混合状态等）发生变化时，调用 `walk()` 沿对应的边前进。
2. 检查当前节点是否有缓存的管线状态对象。
3. 若无缓存，先尝试 `scanForMatchingNode()` 搜索匹配。
4. 若仍未找到，创建新的管线状态对象并通过 `setCurrentNodeData()` 缓存。
