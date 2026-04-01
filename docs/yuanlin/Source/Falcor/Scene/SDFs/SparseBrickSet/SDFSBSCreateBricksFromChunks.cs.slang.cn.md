# SDFSBSCreateBricksFromChunks.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSCreateBricksFromChunks.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,从基元构建流程中的块坐标创建砖块。为每个砖块评估基元(可选合并已有距离场),生成 AABB、间接寻址条目和砖块纹理数据。支持可选的 BC4 压缩。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gParamBlock.primitives` | `StructuredBuffer<SDF3DPrimitive>` | 基元列表 |
| `gParamBlock.chunkCoords` | `ByteAddressBuffer` | 块坐标 |
| `gParamBlock.brickAABBs` | `RWStructuredBuffer<AABB>` | 输出 AABB |
| `gParamBlock.indirectionBuffer` | `RWTexture3D<uint>` | 输出间接寻址 |
| `gParamBlock.bricks` | `RWTexture2D` | 输出砖块数据(float 或 uint2 压缩) |
| `gParamBlock.sdfGrid` | `Texture3D<float>` | 已有距离场(合并模式) |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `COMPRESS_BRICKS` | 启用 BC4 压缩 |
| `_BUILD_WITH_SD_FIELD` | 与已有距离场合并 |
| `BRICK_WIDTH`, `CHUNK_WIDTH` | 砖块和块宽度 |

## 依赖关系

### import
- `Scene.SDFs.SDF3DPrimitive`, `Scene.SDFs.SparseBrickSet.BC4Encode`
- `Utils.Math.AABB`, `Utils.Math.PackedFormats`

## 实现细节

- 线程组大小 256x1x1,每个线程处理一个完整砖块
- 逐体素评估所有基元的距离值,归一化到 `[-1, 1]`(半体素对角线)
- 归一化公式: `clamp(sd * 2 * gridWidth / sqrt(3), -1, 1)`
- BC4 压缩模式下,将 4x4 块转为 snorm8 后调用 `compressBlock`
- 砖块纹理坐标由 `brickID` 线性排列计算
