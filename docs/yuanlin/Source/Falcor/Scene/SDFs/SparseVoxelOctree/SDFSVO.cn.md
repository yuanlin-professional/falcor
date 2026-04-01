# SDFSVO 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVO.h`, `SDFSVO.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

`SDFSVO`(SDF 稀疏体素八叉树)是 `SDFGrid` 的实现之一,将稀疏体素组织为八叉树结构。距离值归一化到 `[-1, 1]`,其中 1 代表半个体素对角线。构建过程包括:统计表面体素、逐级构建哈希表、排序位置码、写入 SVO 偏移、最终构建八叉树。

**注意**: 此实现需要 NVAPI 支持(用于 64 位原子比较交换操作)。

## 类与接口

### `SDFSVO`

- **继承**: `SDFGrid`
- **职责**: 管理稀疏体素八叉树的 GPU 构建和渲染资源

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<SDFSVO> create(ref<Device>)` | 工厂方法 |
| `uint32_t getSVOIndexBitCount() const` | 返回 SVO 索引所需的位数 |
| `size_t getSize() const` | 返回 SVO 缓冲区和 AABB 缓冲区总大小 |
| `void createResources(RenderContext*, bool)` | 多步构建流程(不支持从基元创建) |
| `const ref<Buffer>& getAABBBuffer() const` | 返回单位 AABB 缓冲区(共享) |
| `uint32_t getAABBCount() const` | 返回 1(单个 AABB 包围整个八叉树) |
| `void bindShaderData(const ShaderVar&) const` | 绑定 SVO 缓冲区和层级数 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mValues` | `vector<int8_t>` | CPU 端归一化距离值 |
| `mLevelCount` | `uint32_t` | 八叉树层级数 |
| `mSVOElementCount` | `uint32_t` | SVO 中的总元素数 |
| `mpSVOBuffer` | `ref<Buffer>` | GPU 上的 SVO 数据缓冲区 |
| `mpHashTableBuffer` | `ref<Buffer>` | 构建用哈希表缓冲区 |
| `mpLocationCodesBuffer` | `ref<Buffer>` | 位置码缓冲区 |

## 依赖关系

### 本文件引用
- `Scene/SDFs/SDFGrid.h`, `Core/API/Buffer.h`, `Core/API/Texture.h`, `Core/Pass/ComputePass.h`
- 计算着色器: `SDFSVOBuildLevelFromTexture.cs.slang`, `SDFSVOBuildOctree.cs.slang`, `SDFSVOLocationCodeSorter.cs.slang`, `SDFSVOWriteSVOOffsets.cs.slang`

## 实现细节

- 构建流程:
  1. 统计表面体素数(使用 `SDFSurfaceVoxelCounter`)
  2. 构建最细层级哈希表(记录包含表面的体素)
  3. 自底向上构建其他层级(父节点在有子节点时创建)
  4. 对位置码进行双调排序(Bitonic Sort)
  5. 将排序后的位置写入哈希表的 SVO 偏移
  6. 从哈希表构建最终八叉树
- 使用 `SharedCache` 在实例间共享单位 AABB 缓冲区
- 哈希表容量为最坏情况体素总数的上取 2 的幂
- 通过 Fence 和暂存缓冲区实现 GPU->CPU 数据回读
