# IntersectionHelpers.slang 源码文档
> 路径: `Source/Falcor/Utils/Geometry/IntersectionHelpers.slang`
> 类型: Slang 着色器
> 模块: Utils/Geometry

## 功能概述
提供各种几何图元的射线相交测试函数，包括球体、AABB、三角形、圆盘、圆柱体和圆锥体等。

## 主要函数

### 射线-球体相交

#### `intersectRaySphere`
```slang
bool intersectRaySphere(
    const float3 rayOrigin,
    const float3 rayDir,
    const float3 sphereCenter,
    const float sphereRadius,
    out float t,
    const float tMin = 0.f,
    const float tMax = 1e36f,
    const bool backfaceCull = false
)
```
- **功能**: 射线与球体的相交测试
- **参数**:
  - `rayOrigin`: 射线原点
  - `rayDir`: 射线方向（不需要归一化）
  - `sphereCenter`: 球心
  - `sphereRadius`: 球半径
  - `t`: 输出最近交点距离
  - `tMin`, `tMax`: 有效相交距离范围
  - `backfaceCull`: 是否背面剔除
- **返回**: 是否相交
- **实现**: 基于 Ray-Tracing Gems 第7章的改进算法

### 射线-AABB相交

#### `intersectRayAABB`
```slang
bool intersectRayAABB(
    const float3 rayOrigin,
    const float3 rayDir,
    const float3 aabbMin,
    const float3 aabbMax,
    out float2 nearFar
)
```
- **功能**: 射线与轴对齐包围盒的相交测试
- **参数**:
  - `nearFar`: 输出沿射线的相交区间
- **返回**: 是否相交

### 射线-三角形相交

#### `intersectRayTriangle`
```slang
bool intersectRayTriangle(
    const float3 rayOrigin,
    const float3 rayDir,
    const float3 vertices[3],
    out float3 barycentrics,
    out float t
)
```
- **功能**: 射线与三角形的水密相交测试
- **参数**:
  - `vertices`: 三角形三个顶点
  - `barycentrics`: 输出重心坐标
  - `t`: 输出相交距离
- **返回**: 是否相交
- **实现**: 基于 "Watertight Ray/Triangle Intersection" 论文

### 射线-圆盘相交

#### `intersectRayDisk`
```slang
bool intersectRayDisk(
    const float3 rayOrigin,
    const float3 rayDir,
    const float3 center,
    const float3 normal,
    const float radius,
    out float t
)
```
- **功能**: 射线与圆盘的相交测试
- **参数**:
  - `center`: 圆盘中心
  - `normal`: 圆盘法线
  - `radius`: 圆盘半径
- **返回**: 是否相交

### 射线-圆柱体相交

#### `intersectRayCylinder`
```slang
bool intersectRayCylinder(
    const float3 rayOrigin,
    const float3 rayDir,
    const float3 center,
    const float3 axis,
    const float radius,
    const float height,
    out float t
)
```
- **功能**: 射线与有限圆柱体的相交测试
- **参数**:
  - `center`: 圆柱体底面中心
  - `axis`: 圆柱体轴向（归一化）
  - `radius`: 圆柱体半径
  - `height`: 圆柱体高度
- **返回**: 是否相交

### 射线-圆锥体相交

#### `intersectRayCone`
```slang
bool intersectRayCone(
    const float3 rayOrigin,
    const float3 rayDir,
    const float4 sphereA,
    const float4 sphereB,
    out float2 result
)
```
- **功能**: 射线与圆锥台（两个球体定义）的相交测试
- **参数**:
  - `sphereA`, `sphereB`: 圆锥台两端的球体（xyz=位置, w=半径）
  - `result`: 输出相交距离和参数化坐标
- **返回**: 是否相交
- **实现**: 通过坐标变换将圆锥台映射到标准圆锥进行求解

## 依赖关系

### 本文件引用
- `Utils/Math/MathConstants.slangh`
- `Utils.Math.MathHelpers`

### 被以下文件引用
- 光线追踪着色器
- 碰撞检测系统
- 拾取和选择功能
- 物理模拟模块

## 实现细节

### 数值稳定性
所有相交测试都经过优化以提高浮点精度，特别是球体相交使用改进的判别式计算方法，避免数值不稳定。

### 性能优化
- 早期退出策略：在明显不相交的情况下快速返回
- 最小化分支：使用数学技巧减少条件判断
- 向量化计算：充分利用GPU的SIMD能力

### 特殊情况处理
- 射线原点在图元表面或内部的情况
- 退化几何体（零半径、零高度等）
- 浮点数边界情况（无穷大、NaN等）
