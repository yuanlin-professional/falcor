# LightBVHTypes.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVHTypes.slang`
> 类型: Slang 着色器文件（CPU/GPU 共享）
> 模块: Rendering/Lights

## 功能概述

本文件定义了光源 BVH 的核心数据类型，包括节点的解包格式（`SharedNodeAttributes`、`InternalNode`、`LeafNode`）和压缩存储格式（`PackedNode`）。`PackedNode` 将 BVH 节点压缩为 32 字节（2 个 `uint4`），使用半精度浮点存储 AABB 范围、量化存储锥角和八面体编码存储锥方向。这些类型在 CPU 构建和 GPU 遍历/refit 之间共享。

## 结构体与接口

### `SharedNodeAttributes`

- **职责**: 叶节点和内部节点共享的解包属性

| 成员 | 类型 | 说明 |
|------|------|------|
| `origin` | `float3` | 节点 AABB 中心点 |
| `extent` | `float3` | 节点 AABB 半范围（min/max = origin +/- extent） |
| `flux` | `float` | 总发射辐射通量 |
| `cosConeAngle` | `float` | 法线包围锥余弦半角，`kInvalidCosConeAngle`(-1) 表示无效 |
| `coneDirection` | `float3` | 法线包围锥方向 |

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `void getAABB(out float3 aabbMin, out float3 aabbMax)` | 从 origin/extent 计算 AABB min/max |
| `void setAABB(float3 aabbMin, float3 aabbMax)` | 从 min/max 设置 origin/extent |

### `InternalNode`

- **职责**: 解包的内部节点

| 成员 | 类型 | 说明 |
|------|------|------|
| `attribs` | `SharedNodeAttributes` | 共享属性 |
| `rightChildIdx` | `uint` | 右子节点索引（左子紧邻当前节点存储） |

### `LeafNode`

- **职责**: 解包的叶节点

| 成员 | 类型 | 说明 |
|------|------|------|
| `attribs` | `SharedNodeAttributes` | 共享属性 |
| `triangleCount` | `uint` | 叶节点中的三角形数量 |
| `triangleOffset` | `uint` | 三角形索引列表中的偏移 |

### `PackedNode`

- **职责**: 压缩存储的 BVH 节点（32 字节 = 2 个 `uint4`）

#### 存储布局

- `data[0].x`: MSB 为节点类型位（0=内部，1=叶），剩余位为右子索引或三角形数量+偏移
- `data[0].yzw`: origin (float3)
- `data[1].x`: extent.xy (2x float16)
- `data[1].y`: extent.z (float16) + cosConeAngle (量化 16 位)
- `data[1].z`: coneDirection (八面体编码 2x16 位)
- `data[1].w`: flux (float32)

#### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kTriangleCountBits` | 4 | 三角形计数占用位数（最多 16 个） |
| `kTriangleOffsetBits` | 27 | 三角形偏移占用位数 |

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `bool isLeaf()` | 通过检查 MSB 判断节点类型 |
| `InternalNode getInternalNode()` | 解包内部节点 |
| `LeafNode getLeafNode()` | 解包叶节点 |
| `SharedNodeAttributes getNodeAttributes()` | 解包共享属性 |
| `void setInternalNode(const InternalNode)` | 压缩并写入内部节点 |
| `void setLeafNode(const LeafNode)` | 压缩并写入叶节点 |
| `void setNodeAttributes(const SharedNodeAttributes)` | 压缩并写入共享属性 |

## 实现细节

- 全局常量 `kInvalidCosConeAngle = -1.0f` 表示无效的锥角
- 压缩格式使用有损量化：extent 为 float16（可能丢失精度），cosConeAngle 量化为 15 位
- 可通过定义 `USE_UNCOMPRESSED_NODES` 使用未压缩的 3x`uint4`（48 字节）格式进行调试
- 八面体编码通过 `encodeNormal2x16` / `decodeNormal2x16` 进行方向向量的 32 位紧凑编码

## 依赖关系

### import 引用

- `Utils/HostDeviceShared.slangh` — CPU/GPU 共享基础设施
- `Utils/Math/PackedFormats.h` / `Utils.Math.PackedFormats` — 压缩格式工具（八面体编码等）
