# SDFSBS 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBS.h`, `SDFSBS.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

`SDFSBS`(SDF 稀疏砖块集)是 `SDFGrid` 的实现之一,将空间划分为 NxNxN 的砖块,每个砖块是密集体素集合。支持从距离场数据或基元构建,支持基元烘焙、动态编辑和可选的 BC4 有损压缩。是功能最完整的 SDF 网格实现。

## 类与接口

### `SDFSBS`

- **继承**: `SDFGrid`
- **职责**: 管理稀疏砖块集的完整生命周期,包括从距离场构建、从基元构建、动态更新和压缩

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<SDFSBS> create(ref<Device>, uint32_t brickWidth, bool compressed, uint32_t defaultGridWidth)` | 工厂方法 |
| `UpdateFlags update(RenderContext*)` | 更新网格(处理基元烘焙和动态编辑) |
| `void createResources(RenderContext*, bool)` | 创建 GPU 资源(支持从距离场或基元构建) |
| `void bindShaderData(const ShaderVar&) const` | 绑定间接纹理、砖块纹理、采样器和参数 |
| `float getResolutionScalingFactor() const` | 获取分辨率缩放因子 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mSDField` | `vector<int8_t>` | CPU 端距离场数据 |
| `mBrickWidth` | `uint32_t` | 砖块宽度(体素数) |
| `mCompressed` | `bool` | 是否启用 BC4 压缩 |
| `mBrickCount` | `uint32_t` | 有效砖块数量 |
| `mpBrickAABBsBuffer` | `ref<Buffer>` | 砖块 AABB 缓冲区 |
| `mpIndirectionTexture` | `ref<Texture>` | 间接寻址 3D 纹理(虚拟砖块坐标 -> 砖块 ID) |
| `mpBrickTexture` | `ref<Texture>` | 砖块数据 2D 纹理 |

## 依赖关系

### 本文件引用
- `Scene/SDFs/SDFGrid.h`, `Core/Pass/ComputePass.h`, `Utils/Algorithm/PrefixSum.h`
- 多个计算着色器(见下方列表)

### 计算通道着色器
- `SDFSBSAssignBrickValidityFromSDFieldPass.cs.slang` - 从距离场标记砖块有效性
- `SDFSBSResetBrickValidity.cs.slang` - 重置砖块有效性
- `SDFSBSCopyIndirectionBuffer.cs.slang` - 复制间接寻址缓冲区到纹理
- `SDFSBSCreateBricksFromSDField.cs.slang` - 从距离场创建砖块
- `SDFSBSCreateChunksFromPrimitives.cs.slang` - 从基元创建块
- `SDFSBSCompactifyChunks.cs.slang` - 压缩块数据
- `SDFSBSPruneEmptyBricks.cs.slang` - 剪枝空砖块
- `SDFSBSCreateBricksFromChunks.cs.slang` - 从块创建砖块
- `SDFSBSComputeIntervalSDFieldFromGrid.cs.slang` - 计算区间距离场
- `SDFSBSExpandSDFieldData.cs.slang` - 扩展距离场数据

## 实现细节

- 支持两种构建路径:
  1. 从距离场(sdfg 文件)构建:使用前缀和分配砖块 ID,然后填充砖块数据
  2. 从基元构建:使用区间算术逐级细分,通过粗/细剪枝排除空砖块
- BC4 压缩要求 `brickWidth + 1` 为 4 的倍数
- 基元烘焙通过将基元评估结果合并到距离场纹理实现
- 动态编辑支持网格分辨率自动扩展
- 使用 `PrefixSum` 算法实现高效的砖块 ID 分配
