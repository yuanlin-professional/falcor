# SDFSVSVoxelizer.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelSet/SDFSVSVoxelizer.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseVoxelSet

## 功能概述

计算着色器,将 SDF 网格纹理体素化为稀疏体素集。检测包含隐式表面的体素,为每个表面体素生成 AABB 和带有 4x4 值切片的体素数据,同时计算邻居有效性掩码。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float safeLoadValue(int3 coord)` | 安全加载纹理值(越界返回 1.0) |
| `static uint4 packValues(float4, float4, float4, float4)` | 将 4x4 切片值打包为 snorm8 格式 |
| `uint4 loadAndPackSlice(int3 voxelCoords, int x)` | 加载指定 x 偏移的 4x4 切片并打包 |
| `bool voxelContainsSurface(int3 voxelCoords)` | 检测体素是否包含表面 |
| `uint createNeighborValidityMask(int3 voxelCoords)` | 检测 27 个邻居的有效性 |
| `void main(...)` | 主入口,使用组共享内存批量写入输出缓冲区 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelTypes`, `Scene.SDFs.SDFVoxelCommon`
- `Utils.Math.AABB`, `Utils.Math.FormatConversion`

## 实现细节

- 线程组大小 4x4x4,使用组共享内存累积组内有效体素
- 每个有效体素加载 4 个 4x4 x 方向切片(包含邻居数据),用于后续梯度计算
- 组管理线程(groupThreadID == 0)通过 `IncrementCounter` 原子操作分配全局索引并批量写入
- AABB 范围从体素坐标转换为 `[-0.5, 0.5]` 局部空间
