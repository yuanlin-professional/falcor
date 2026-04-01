# DisplacementMapping.slang 源码文档

> 路径: `Source/Falcor/Scene/Displacement/DisplacementMapping.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Displacement

## 功能概述

实现完整的位移贴图（Displacement Mapping）光线求交系统。通过 Slang 的 `extension` 机制扩展 `DisplacementData` 结构体，添加值映射、纹理采样、外壳包围盒计算等方法。同时实现了基于棱柱体（prism）的光线-位移三角形求交算法，包括：

1. 光线与位移外壳（shell）的求交
2. 纹理空间中的高度场光线步进（ray marching）
3. 位移后的表面法线计算

核心算法参考了 Alexander Reshetov 的"Cool Patches"双线性面片求交方法。

## 编译宏定义

| 宏 | 默认值 | 说明 |
|-----|--------|------|
| `DISPLACEMENT_DISABLED` | `0` | 调试用：设为 1 时禁用位移贴图，改为对基础三角形做软件求交 |
| `DISPLACEMENT_TWO_SIDED` | `0` | 设为 1 时位移表面为双面（可能产生裂缝） |
| `DISPLACEMENT_DEBUG_DISPLAY_SHELL` | `0` | 调试用：设为 1 时显示外壳而非位移表面 |

## 静态常量

| 常量 | 类型 | 值 | 说明 |
|------|------|-----|------|
| `kShellForceMaxThickness` | `bool` | `false` | 是否强制使用最大厚度的外壳 |
| `kShellBoundsCalcUseSampleGrad` | `bool` | `true` | 外壳包围盒计算是否使用 SampleGrad（更精确） |
| `kHitFaceNormalUseCentralGrad` | `bool` | `true` | 命中面法线是否使用中心差分梯度 |
| `kDisplacementScalingUsePreciseLength` | `bool` | `false` | 位移缩放是否使用精确的法线长度插值 |
| `kSurfaceThickness` | `float` | `0.008` | 表面厚度（用于双面模式） |
| `kRaymarchingMaxSampleCount` | `int` | `32` | 光线步进最大采样次数 |
| `kRaymarchingSampleCountFactor` | `int` | `2` | 光线步进采样次数倍率 |

## 结构体与接口

### `extension DisplacementData`

通过 Slang 的 `extension` 机制为 `DisplacementData` 添加方法。

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `float mapValue(float raw)` | 将原始纹理值映射为位移值：`scale * (raw + bias)` |
| `float2 mapValue(float2 raw)` | 同上，float2 重载 |
| `float4 mapValue(float4 raw)` | 同上，float4 重载 |
| `float readValue(float2 texelPos, float lod = 0)` | 在指定纹素位置和 LOD 级别读取位移值 |
| `float4 readValueMinMax(float2 texelPos, float lod = 0)` | 读取包含最小/最大值的位移值（4 通道） |
| `float getConservativeGlobalExpansion()` | 获取保守的全局扩展距离（全局最大位移绝对值） |
| `float2 getGlobalMinMax()` | 获取全局最小/最大位移值（假设纹理值范围 [0,1]） |
| `float2 getShellMinMax(float2 texCrd0, float2 texCrd1, float2 texCrd2)` | 计算三角形局部外壳的最小/最大高度，使用 min/max 采样器进行保守估计 |
| `float getTriangleConservativeMipLevel(float2 texCrd0, float2 texCrd1, float2 texCrd2)` | 计算覆盖三角形 UV 范围的保守 MIP 级别 |

### `DisplacementIntersection`

位移求交结果结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `barycentrics` | `float2` | 重心坐标 (u, v) |
| `displacement` | `float` | 交点处的位移值 |
| `t` | `float` | 光线参数 t |

| 方法 | 说明 |
|------|------|
| `float3 getBarycentricWeights()` | 返回三分量重心权重 `(1-u-v, u, v)` |

### `IntersectAttribute`

光线-几何求交的详细属性结构体，用于棱柱体求交的中间结果。

| 成员 | 类型 | 说明 |
|------|------|------|
| `intersected` | `bool` | 是否发生相交 |
| `t` | `float` | 光线参数 t |
| `u`, `v` | `float` | 三角形重心坐标或双线性面片 UV 坐标 |
| `barycentric` | `float3` | 重心坐标 |
| `textureSpaceHeight` | `float` | 纹理空间中的高度值 |
| `hitNormal` | `float3` | 命中面法线（仅对三角形有效） |
| `backFace` | `bool` | 是否为背面命中 |

| 方法 | 说明 |
|------|------|
| `bool max(IntersectAttribute other)` | 取 t 值更大的交点（出射点） |
| `bool min(IntersectAttribute other)` | 取 t 值更小的交点（入射点） |
| `void updateTriangleBarycentricAndHeight(float texSpaceHeight)` | 更新三角形求交的重心和高度 |
| `void updateBilinearPatchBarycentricAndHeight(...)` | 更新双线性面片求交的重心和高度（通过线性插值） |

### `TriangleVertex`

三角形顶点辅助结构体，用于法线计算中的顶点插值。

| 成员 | 类型 | 说明 |
|------|------|------|
| `position` | `float3` | 顶点位置 |
| `normal` | `float3` | 顶点法线 |
| `texCrd` | `float2` | 纹理坐标 |

| 方法 | 说明 |
|------|------|
| `static TriangleVertex interpolate(vertices[3], barycentrics, normalizeNormal)` | 使用重心坐标插值三角形顶点属性 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `bool calcDebugTriangleIntersection(Ray ray, float3 positions[3], out DisplacementIntersection result)` | 调试用：对基础三角形做标准光线求交（不含位移） |
| `void rayTriangleIntersectionTest(float3 p0, p1, p2, rayOrigin, rayDir, triangleHeight, out IntersectAttribute)` | 光线-三角形求交测试，记录详细属性 |
| `float solveT(float3 r, float3 q, float3 p)` | 根据光线起点 r、方向 q 和目标点 p 反算光线参数 t |
| `float copySign(float x, float y)` | 将 x 的绝对值赋予 y 的符号 |
| `void rayBilinearPatchIntersectionTest(float3 q00..q11, p0Bary, p1Bary, rayOrigin, rayDir, rayTmax, minHeight, maxHeight, out attri0, out attri1)` | 光线-双线性面片求交（基于 Reshetov 的 "Cool Patches" 算法），最多返回两个交点 |
| `bool traceHeightMapEstimated(DisplacementData, float3 startPoint, endPoint, out intersectedT, out intersectedHeight)` | 在纹理空间中进行光线步进（ray marching），通过逐步采样高度场检测光线与位移表面的交点 |
| `bool calcDisplacementIntersection(Ray ray, StaticVertexData vertices[3], DisplacementData, out DisplacementIntersection)` | 计算光线与位移三角形的完整求交：构建外壳棱柱、求入射/出射点、转换到纹理空间、光线步进 |
| `bool intersectDisplacedTriangle(Ray ray, StaticVertexData vertices[3], float4x4 worldMat, DisplacementData, out DisplacementIntersection)` | 顶层接口：将顶点变换到世界空间后调用 `calcDisplacementIntersection`，并将位移值转换为世界空间距离 |
| `float3 computeDisplacedTriangleNormal(float3 barycentrics, StaticVertexData vertices[3], float4x4 worldMat, float3x3 worldInvTransposeMat, DisplacementData)` | 计算位移表面的法线。使用有限差分法（前向或中心差分）在重心坐标空间中采样相邻点，通过叉积得到法线 |

## 依赖关系

### import

- `Scene.SceneTypes`：`StaticVertexData` 静态顶点数据结构
- `Scene.Material.BasicMaterialData`：基础材质数据
- `Utils.Math.Ray`：`Ray` 光线结构体
- `Utils.Geometry.IntersectionHelpers`：`intersectRayTriangle` 光线-三角形求交工具
- `Scene.Displacement.DisplacementData`：`DisplacementData` 数据结构定义

### 被以下文件引用

- `DisplacementUpdate.cs.slang`：引用 `DisplacementMapping` 以使用 `getShellMinMax` 等方法
- 光线追踪渲染管线：在 any-hit / intersection shader 中调用 `intersectDisplacedTriangle`
- 着色阶段：调用 `computeDisplacedTriangleNormal` 获取位移法线

## 实现细节

### 光线-位移三角形求交算法

整体流程分为三个阶段：

**阶段 1：光线-棱柱体求交**
- 根据三角形的三个顶点和法线，按外壳高度 (shellMinMax) 构建挤出三角形和内缩三角形
- 对挤出三角形（顶面）、三个双线性侧面片、内缩三角形（底面）分别做光线求交
- 取所有交点中 t 最小者为入射点 (minIntersection)，t 最大者为出射点 (maxIntersection)

**阶段 2：坐标空间转换**
- 将入射/出射点的重心坐标转换为纹理坐标
- 构建纹理空间中的起点和终点（xy = 纹素坐标，z = 高度值）

**阶段 3：纹理空间光线步进**
- 在纹理空间中从起点到终点进行均匀步进
- 每步采样位移纹理，比较光线高度与纹理高度
- 检测到穿越时，使用线性插值估算精确交点位置

### 双线性面片求交

使用 Reshetov 的 "Cool Patches" 几何方法：
- 将光线与双线性面片的求交化为求解二次方程 `a + b*u + c*u^2 = 0`
- 最多产生两个有效交点（进入和离开棱柱侧面）
- 参考: Ray Tracing Gems, Chapter 8

### 法线计算

使用有限差分法：
- 在重心坐标空间中沿两个方向各偏移一小步
- 对偏移后的点进行顶点属性插值并采样位移
- 通过叉积计算位移后的表面法线
- 支持前向差分和中心差分两种模式（由 `kHitFaceNormalUseCentralGrad` 控制）
