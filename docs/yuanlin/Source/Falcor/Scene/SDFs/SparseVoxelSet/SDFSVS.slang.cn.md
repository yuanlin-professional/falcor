# SDFSVS.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelSet/SDFSVS.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs/SparseVoxelSet

## 功能概述

稀疏体素集的 GPU 端实现。实现 `ISDFGrid` 接口,支持光线与稀疏体素集的交叉测试。每个体素存储 4x4 值切片,支持从中解包中心 8 角点值和边缘值用于梯度计算。

## 结构体与接口

### `SDFSVS`

- **继承**: `SDFGridBase`, `ISDFGrid`

#### 成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `aabbs` | `StructuredBuffer<AABB>` | 体素 AABB 缓冲区 |
| `voxels` | `StructuredBuffer<SDFSVSVoxel>` | 体素数据缓冲区 |
| `virtualGridWidth` | `uint` | 虚拟网格宽度 |
| `normalizationFactor` | `float` | 归一化因子 |

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `void unpackSliceValues(...)` | 从 4x4 切片解包指定位置的 8 个角点值 |
| `void unpackCenterValues(...)` | 从两个切片解包中心 8 个角点值 |
| `bool intersectRayAABBWithInsideCheck(...)` | AABB 交叉测试,同时检测原点是否在 AABB 内 |
| `bool intersectSDF(...)` | 光线-SDF 交叉测试 |
| `bool intersectSDFAny(...)` | 仅判断相交 |
| `float3 calculateGradient(float3, uint)` | 计算命中点梯度 |

## 依赖关系

### import
- `Scene.SDFs.SDFGridBase`, `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SDFVoxelTypes`
- `Scene.SDFs.SDFVoxelHitUtils`, `Utils.Math.AABB`, `Utils.Geometry.IntersectionHelpers`

## 实现细节

- 使用 `primitiveID` 直接索引体素和 AABB
- 将光线方向归一化后进行 AABB 交叉测试
- 在体素局部空间中调用 `SDFVoxelHitUtils::intersectSDFVoxel` 进行精确求解
- 梯度计算仅支持不连续数值梯度模式
