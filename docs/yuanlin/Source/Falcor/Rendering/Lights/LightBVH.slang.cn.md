# LightBVH.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVH.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件定义了光源 BVH 的 GPU 端数据结构，提供只读版本 `LightBVH` 和可写版本 `RWLightBVH`。两者封装了 BVH 节点缓冲区、三角形索引缓冲区和遍历位掩码缓冲区，提供节点类型判断、节点解包和三角形索引查询等方法。`LightBVH` 用于采样遍历，`RWLightBVH` 用于 BVH refit（节点边界更新）。

## 结构体与接口

### `LightBVH` (只读版本)

- **职责**: GPU 端光源 BVH 只读访问

| 成员 | 类型 | 说明 |
|------|------|------|
| `nodes` | `StructuredBuffer<PackedNode>` | BVH 节点缓冲区，根节点在索引 0，标记为 `[root]` |
| `triangleIndices` | `StructuredBuffer<uint>` | 发光三角形索引缓冲区，叶节点引用连续范围 |
| `triangleBitmasks` | `StructuredBuffer<uint2>` | 每三角形的遍历位掩码（0=左子，1=右子） |

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `bool isLeaf(uint nodeIndex)` | 判断指定节点是否为叶节点 |
| `LeafNode getLeafNode(uint nodeIndex)` | 解包叶节点 |
| `InternalNode getInternalNode(uint nodeIndex)` | 解包内部节点 |
| `SharedNodeAttributes getNodeAttributes(uint nodeIndex)` | 获取节点共享属性 |
| `uint getNodeTriangleIndex(const LeafNode node, uint index)` | 获取叶节点中指定位置的三角形索引 |

### `RWLightBVH` (可写版本)

- **职责**: GPU 端光源 BVH 可读写访问（用于 refit）

| 成员 | 类型 | 说明 |
|------|------|------|
| `nodes` | `RWStructuredBuffer<PackedNode>` | 可写 BVH 节点缓冲区 |
| `triangleIndices` | `StructuredBuffer<uint>` | 三角形索引（只读） |
| `triangleBitmasks` | `StructuredBuffer<uint2>` | 遍历位掩码（只读） |

#### 额外函数（写入方法）

| 函数签名 | 说明 |
|----------|------|
| `void setLeafNode(uint nodeIndex, LeafNode node)` | 写入叶节点数据 |
| `void setInternalNode(uint nodeIndex, InternalNode node)` | 写入内部节点数据 |

## 依赖关系

### import 引用

- `Utils.Attributes` — 着色器属性
- `LightBVHTypes` — BVH 节点类型定义（导出）
