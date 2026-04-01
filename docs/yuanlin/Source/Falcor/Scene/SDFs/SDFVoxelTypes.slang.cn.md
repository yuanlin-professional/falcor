# SDFVoxelTypes.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFVoxelTypes.slang`
> 类型: Slang 着色器文件 (Host/Device 共享)
> 模块: Scene/SDFs

## 功能概述

定义了各种 SDF 网格实现所使用的体素数据结构。这些结构体在主机端和设备端共享,用于 GPU 缓冲区的数据布局。

## 结构体与接口

### `SDFSVSVoxel`

- **用途**: 稀疏体素集 (SDFSVS) 的体素数据

| 成员 | 类型 | 说明 |
|------|------|------|
| `packedValuesSlices[4]` | `uint4[4]` | 4 个 4x4 的 x 方向值切片,以 8 位 snorm 打包 |
| `validNeighborsMask` | `uint` | 低 27 位用作邻居有效性掩码 |

### `SDFSVOHashTableVoxel`

- **用途**: 稀疏体素八叉树 (SDFSVO) 构建阶段的哈希表体素

| 成员 | 类型 | 说明 |
|------|------|------|
| `locationCode` | `uint2` | 体素位置码(64 位) |
| `svoOffset` | `uint` | 该体素在 SVO 中的偏移 |
| `validMask` | `uint` | 子节点有效性掩码 |
| `packedValues` | `uint2` | 8 个角点值,以 8 位 snorm 打包 |

### `SDFSVOVoxel`

- **用途**: 稀疏体素八叉树 (SDFSVO) 最终存储格式

| 成员 | 类型 | 说明 |
|------|------|------|
| `relationData` | `uint` | 关系数据,编码第一个子节点的 SVO 索引和子节点有效性 |
| `locationCode` | `uint2` | 体素位置码(64 位) |
| `packedValues` | `uint2` | 8 个角点值,以 8 位 snorm 打包 |

## 依赖关系

### include / import
- `Utils/HostDeviceShared.slangh`
- `Utils/Math/AABB.h` (主机端) / `Utils.Math.AABB` (设备端)

### 被以下文件引用
- `SDFSVS.cpp`, `SDFSVS.slang`, `SDFSVSVoxelizer.cs.slang`
- `SDFSVO.cpp`, `SDFSVO.slang`, `SDFSVOBuildOctree.cs.slang`
