# SDFSBS.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBS.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

稀疏砖块集的 GPU 端实现。实现 `ISDFGrid` 接口,使用 DDA(数字微分分析器)算法遍历砖块内的体素进行光线追踪。通过间接纹理将虚拟砖块坐标映射到实际砖块 ID,再从 2D 砖块纹理中加载体素值。

## 结构体与接口

### `SDFSBS`

- **继承**: `SDFGridBase`, `ISDFGrid`

#### 成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `aabbs` | `StructuredBuffer<AABB>` | 砖块 AABB 缓冲区 |
| `indirectionBuffer` | `Texture3D<uint>` | 间接寻址纹理 |
| `bricks` | `Texture2D<float>` | 砖块数据纹理 |
| `sampler` | `SamplerState` | 采样器状态 |
| `virtualGridWidth` | `uint` | 虚拟网格宽度 |
| `brickWidth` | `uint` | 砖块宽度(体素数) |
| `normalizationFactor` | `float` | 归一化因子 |

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `bool calculateBrickCoords(...)` | 从命中点计算砖块坐标、纹理坐标和体素单位坐标 |
| `bool calculateBrickTexelCoords(uint3, out uint2)` | 通过间接纹理查找砖块纹素坐标 |
| `void loadVoxelValues(uint2, int3, out float4, out float4)` | 从砖块纹理加载体素角点值(支持 Gather 优化) |
| `bool intersectSDF(...)` | DDA + 球体追踪的光线-SDF 交叉测试 |
| `bool intersectSDFAny(...)` | 仅判断相交 |
| `void decodeHit(...)` | 解码命中点并计算梯度 |

## 依赖关系

### import
- `Scene.SDFs.SDFGridBase`, `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SDFVoxelHitUtils`
- `Utils.Math.AABB`, `Utils.Math.FormatConversion`, `Utils.Math.PackedFormats`
- `Utils.Geometry.IntersectionHelpers`

## 实现细节

- 使用 DDA 算法在砖块内遍历体素:
  1. 从命中点的 AABB 计算砖块坐标和体素坐标
  2. DDA 逐步推进,对包含表面的体素调用 `SDFVoxelHitUtils::intersectSDFVoxel`
  3. 通过 `stepSign` 和 `tDeltas` 控制 DDA 步进方向和距离
- 支持 `Gather` 优化(通过 `USE_GATHER` 宏),一次纹理采样获取 4 个值
- 砖块空间可能大于体素空间(当 `virtualBricksPerAxis * brickWidth > virtualGridWidth` 时)
- 光线方向分量被钳制到 `FLT_EPSILON` 以避免除零
