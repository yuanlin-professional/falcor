# Grid.slang 源码文档

> 路径: `Source/Falcor/Scene/Volume/Grid.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Volume

## 功能概述

GPU 端的体素网格访问结构体，基于 PNanoVDB（NanoVDB 的 HLSL 移植版）。提供世界空间和索引空间之间的坐标转换、多种采样方法（最近邻、三线性、随机三线性）以及 DDA（数字微分分析）光线行进遍历器。

## 结构体与接口

### `Grid`

| 成员 | 类型 | 说明 |
|------|------|------|
| `minIndex` | `int3` | 最小索引 |
| `minValue` | `float` | 最小值 |
| `maxIndex` | `int3` | 最大索引 |
| `maxValue` | `float` | 最大值 |
| `buf` | `StructuredBuffer<uint>` | NanoVDB 数据缓冲区 |
| `rangeTex` | `Texture3D<float2>` | 砖块范围纹理（majorant/minorant） |
| `indirectionTex` | `Texture3D<uint4>` | 砖块间接寻址纹理 |
| `atlasTex` | `Texture3D<float>` | 砖块图集纹理 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `int3 getMinIndex()` | 获取最小索引 |
| `int3 getMaxIndex()` | 获取最大索引 |
| `float getMinValue()` | 获取最小值 |
| `float getMaxValue()` | 获取最大值 |
| `float getMeanValue()` | 获取平均值 |
| `float getStdDevValue()` | 获取标准差 |
| `float3 worldToIndexPos(float3 pos)` | 世界空间位置转索引空间 |
| `float3 worldToIndexDir(float3 dir)` | 世界空间方向转索引空间（归一化） |
| `float3 worldToIndexDirUnnormalized(float3 dir)` | 世界空间方向转索引空间（不归一化） |
| `float3 indexToWorldPos(float3 pos)` | 索引空间位置转世界空间 |
| `float3 indexToWorldDir(float3 dir)` | 索引空间方向转世界空间（归一化） |
| `Accessor createAccessor()` | 创建网格访问器 |
| `float lookupWorld(float3 pos, inout Accessor)` | 世界空间最近邻查找 |
| `float lookupIndex(int3 index, inout Accessor)` | 索引空间最近邻查找（NanoVDB） |
| `float lookupIndexTex(int3 index)` | 索引空间最近邻查找（砖块纹理） |
| `float lookupIndexLocalMajorantTex(int3 index, int mip)` | 查找指定 mip 级别的局部 majorant |
| `float lookupLinearWorld(float3 pos, inout Accessor)` | 世界空间三线性插值查找 |
| `float lookupLinearIndex(float3 index, inout Accessor)` | 索引空间三线性插值查找（NanoVDB） |
| `float lookupLinearIndexTex(float3 index)` | 索引空间三线性插值查找（砖块纹理） |
| `float lookupStochasticWorld(float3 pos, float3 u, inout Accessor)` | 世界空间随机三线性采样 |
| `float lookupStochasticIndex(float3 index, float3 u, inout Accessor)` | 索引空间随机三线性采样（NanoVDB） |
| `float lookupStochasticIndexTex(float3 index, float3 u)` | 索引空间随机三线性采样（砖块纹理） |

### `Grid::DDA`

嵌套结构体，用于在 VDB 树中进行 DDA 光线行进。

| 枚举 `Level` | 说明 |
|-------|------|
| `Voxel = 1` | 体素级别遍历 |
| `Leaf = 8` | 叶节点级别遍历 |

| 方法 | 说明 |
|------|------|
| `void initialize(Grid, inout Accessor, float3 pos, float3 dir, float tmin, float tmax, Level)` | 初始化 DDA 遍历 |
| `bool isActive(Grid, inout Accessor, pnanovdb_coord_t ijk)` | 检查指定位置是否活跃 |
| `bool step(Grid, inout Accessor, inout bool hasData, inout float dt)` | 执行一步 DDA，返回步长和数据状态 |
| `float getStepData(Grid, inout Accessor)` | 获取当前步的网格数据 |

## 依赖关系 / import

- `nanovdb/PNanoVDB.h` — PNanoVDB HLSL 实现（通过 `#define PNANOVDB_HLSL` 启用）

## 实现细节

- 支持两种数据路径：NanoVDB 缓冲区（通过 PNanoVDB 访问器）和砖块纹理图集。
- DDA 遍历支持叶节点级别跳跃，在体积渲染中用于快速跳过空白区域。
- 叶节点级别的 `isActive` 方法手动遍历 VDB 树层级并缓存最低存在节点的地址。
- 随机三线性采样通过随机偏移替代确定性插值，适用于蒙特卡洛体积渲染。
