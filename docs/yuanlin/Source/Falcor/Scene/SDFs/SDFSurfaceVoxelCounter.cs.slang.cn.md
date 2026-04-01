# SDFSurfaceVoxelCounter.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFSurfaceVoxelCounter.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs

## 功能概述

计算着色器,用于统计 SDF 网格纹理中包含隐式表面的体素数量。使用组共享内存优化纹理读取,通过原子操作累加包含表面的体素计数。此通道被 SDFSVS 和 SDFSVO 在资源创建阶段使用。

## 结构体与接口

### 常量缓冲区

| 变量 | 类型 | 说明 |
|------|------|------|
| `gGridWidth` | `uint` | 网格宽度 |

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gSDFGrid` | `Texture3D<float>` | SDF 网格 3D 纹理 |
| `gTotalVoxelCount` | `RWByteAddressBuffer` | 输出的表面体素总计数 |

### 组共享内存

| 变量 | 类型 | 说明 |
|------|------|------|
| `gGroupVoxelCount` | `uint` | 当前组内包含表面的体素数量 |
| `gGroupValues` | `float[(GROUP_WIDTH+1)^3]` | 缓存的角点值 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint getGroupValueIndex(uint3 groupValueCoords)` | 计算组共享内存中的线性索引 |
| `void main(...)` | 主入口,每个线程处理一个体素 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`

## 实现细节

- 线程组大小为 4x4x4
- 使用组共享内存缓存 `(GROUP_WIDTH+1)^3` 个角点值,边界线程负责加载额外的邻居值
- 每个线程从共享内存加载 8 个角点值,调用 `SDFVoxelCommon::containsSurface` 判断是否包含表面
- 使用两级原子操作:组内 `InterlockedAdd` 和组间通过缓冲区的 `InterlockedAdd`
