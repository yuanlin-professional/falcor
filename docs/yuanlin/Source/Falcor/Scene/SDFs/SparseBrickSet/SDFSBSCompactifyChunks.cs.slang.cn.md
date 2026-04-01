# SDFSBSCompactifyChunks.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSCompactifyChunks.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,用于压缩(紧凑化)块坐标数据。在基元构建流程中,经过前缀和计算后,此着色器将有效块的坐标重新排列到连续的内存位置,移除无效块留下的空隙。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gParamBlock.chunkCount` | `uint` | 块总数 |
| `gParamBlock.chunkIndirection` | `ByteAddressBuffer` | 前缀和生成的间接索引 |
| `gParamBlock.chunkValidity` | `ByteAddressBuffer` | 块有效性 |
| `gParamBlock.chunkCoords` | `ByteAddressBuffer` | 源块坐标 |
| `gParamBlock.compactedChunkCoords` | `RWByteAddressBuffer` | 输出压缩后的块坐标 |

## 实现细节

- 线程组大小 256x1x1
- 每个线程处理一个块:检查有效性,读取间接索引,将坐标复制到压缩后的位置
