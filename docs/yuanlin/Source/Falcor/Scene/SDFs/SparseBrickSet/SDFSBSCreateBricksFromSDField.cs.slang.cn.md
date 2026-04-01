# SDFSBSCreateBricksFromSDField.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSCreateBricksFromSDField.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,从已有的 SDF 距离场纹理创建砖块数据。根据间接寻址缓冲区中已分配的砖块 ID,读取距离场纹理值填充砖块纹理,并生成砖块 AABB。支持可选的 BC4 压缩。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gParamBlock.sdfGrid` | `Texture3D<float>` | 源距离场纹理 |
| `gParamBlock.indirectionBuffer` | `Buffer<uint>` | 间接寻址缓冲区 |
| `gParamBlock.brickAABBs` | `RWStructuredBuffer<AABB>` | 输出 AABB |
| `gParamBlock.bricks` | `RWTexture2D` | 输出砖块纹理数据 |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `COMPRESS_BRICKS` | 启用 BC4 压缩 |

## 依赖关系

### import
- `Utils.Math.AABB`, `Utils.Math.FormatConversion`
- `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SparseBrickSet.BC4Encode`

## 实现细节

- 线程组大小 256x1x1,每线程处理一个虚拟砖块
- 跳过 `brickID == UINT32_MAX` 的无效砖块
- 从虚拟砖块 ID 反算 3D 坐标,计算 AABB 和砖块纹理坐标
- 距离值直接从距离场纹理读取(已归一化),越界体素填充 1.0
