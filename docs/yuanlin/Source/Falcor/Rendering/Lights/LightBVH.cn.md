# LightBVH 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVH.h` + `LightBVH.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件实现了光源采样加速结构——光源二叉 BVH（Bounding Volume Hierarchy）。该数据结构基于 Moreau 和 Clarberg 在 Ray Tracing Gems 第 18 章中描述的方法，将所有自发光三角形组织为一棵二叉树。BVH 支持 CPU 端的深度优先遍历以及 GPU 端的随机遍历采样。节点以 `PackedNode` 格式压缩存储，支持在不改变层级结构的情况下进行 refit（重新拟合边界）。

## 类与接口

### `LightBVH`

- **继承**: 无
- **职责**: 管理光源 BVH 数据结构，支持构建、重拟合、遍历和 GPU 绑定

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `LightBVH(ref<Device>, const ref<const ILightCollection>&)` | 构造函数，创建叶节点和内部节点的更新计算通道 |
| `void refit(RenderContext* pRenderContext)` | 重新拟合所有 BVH 节点边界，不改变层级结构 |
| `void traverseBVH(const NodeFunction&, const NodeFunction&, uint32_t)` | 深度优先遍历 BVH，对每个节点执行回调 |
| `void bindShaderData(const ShaderVar& var) const` | 将 BVH 节点缓冲区、三角形索引和位掩码绑定到着色器 |
| `void renderUI(Gui::Widgets& widget)` | 渲染 BVH 统计信息 UI |
| `bool isValid() const` | 检查 BVH 是否已构建并可用 |
| `const BVHStats& getStats() const` | 获取 BVH 统计信息 |

### `LightBVH::BVHStats`

- **职责**: 存储 BVH 的统计信息

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `treeHeight` | `uint32_t` | 树高度（根到最深叶节点的边数） |
| `minDepth` | `uint32_t` | 最浅叶节点深度 |
| `byteSize` | `uint32_t` | BVH 占用字节数 |
| `internalNodeCount` | `uint32_t` | 内部节点数量 |
| `leafNodeCount` | `uint32_t` | 叶节点数量 |
| `triangleCount` | `uint32_t` | 三角形总数 |

### `LightBVH::NodeLocation`

- **职责**: 表示节点在树中的位置

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `nodeIndex` | `uint32_t` | 节点索引 |
| `depth` | `uint32_t` | 节点深度 |

#### 其他关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mNodes` | `std::vector<PackedNode>` | CPU 端压缩 BVH 节点数组 |
| `mNodeIndices` | `std::vector<uint32_t>` | 按深度排序的节点索引数组 |
| `mPerDepthRefitEntryInfo` | `std::vector<RefitEntryInfo>` | 每层的节点偏移和数量（用于 refit） |
| `mpBVHNodesBuffer` | `ref<Buffer>` | GPU 端 BVH 节点缓冲区 |
| `mpTriangleIndicesBuffer` | `ref<Buffer>` | 按叶节点排序的三角形索引缓冲区 |
| `mpTriangleBitmasksBuffer` | `ref<Buffer>` | 每三角形的遍历位掩码缓冲区 |
| `mpNodeIndicesBuffer` | `ref<Buffer>` | 按深度排序的节点索引缓冲区（用于 GPU refit） |

## 实现细节

- **refit()**: 先执行叶节点更新（`updateLeafNodes`），再从最深层到根逐层执行内部节点更新（`updateInternalNodes`），使用 `LightBVHRefit.cs.slang` 计算着色器
- **traverseBVH()**: 使用显式栈进行深度优先遍历，左子节点紧邻当前节点存储，右子节点通过索引引用
- **finalize()**: 在构建完成后调用，计算统计信息并生成按深度排序的节点索引数组
- **uploadCPUBuffers()**: 将 CPU 端数据上传到 GPU 结构化缓冲区，按需重新分配缓冲区
- **syncDataToCPU()**: 将 GPU 端数据同步回 CPU（含 flush，较慢）

## 依赖关系

### 本文件引用

- `LightBVHTypes.slang` — BVH 节点类型定义
- `Core/Macros.h`、`Core/API/Buffer.h`、`Core/Error.h`、`Core/API/RenderContext.h`
- `Scene/Lights/LightCollection.h` — 光源集合
- `Utils/Math/AABB.h`、`Utils/Math/Vector.h` — 数学工具
- `Utils/UI/Gui.h` — GUI 工具
- `Utils/Timing/Profiler.h` — 性能分析（仅 .cpp）
- `Rendering/Lights/LightBVHRefit.cs.slang` — GPU refit 计算着色器

### 被以下文件引用

- `LightBVHBuilder.h` — BVH 构建器作为友元类访问内部数据
- `LightBVHSampler.h` — BVH 采样器管理本类实例
