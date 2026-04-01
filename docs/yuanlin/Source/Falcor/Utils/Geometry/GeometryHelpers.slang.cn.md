# GeometryHelpers.slang 源码文档
> 路径: `Source/Falcor/Utils/Geometry/GeometryHelpers.slang`
> 类型: Slang 着色器
> 模块: Utils/Geometry

## 功能概述
提供光线追踪和几何计算的辅助函数集合，包括射线原点偏移、包围锥计算、曲线采样和球体变换等功能。

## 主要函数

### 射线追踪辅助

#### `computeRayOrigin`
```slang
float3 computeRayOrigin(float3 pos, float3 normal)
```
- **功能**: 基于命中位置计算新的射线原点，避免自相交
- **参数**:
  - `pos`: 射线命中位置
  - `normal`: 命中表面的法线（归一化）
- **返回**: 新射线的原点
- **实现**: 基于 Ray Tracing Gems 第6章的方法
- **可微分**: 支持前向和反向微分（`fwd_computeRayOrigin`, `bwd_computeRayOrigin`）

### 包围锥计算

#### `boundBoxSubtendedConeAngleCenter`
```slang
void boundBoxSubtendedConeAngleCenter(
    const float3 origin,
    const float3 aabbMin,
    const float3 aabbMax,
    out float3 coneDir,
    out float sinTheta,
    out float cosTheta
)
```
- **功能**: 计算从视点看到的包围AABB的最小包围锥
- **参数**:
  - `origin`: 视点原点
  - `aabbMin`: AABB最小角点
  - `aabbMax`: AABB最大角点
  - `coneDir`: 输出锥体轴向量（归一化）
  - `sinTheta`: 输出锥体半角的正弦值
  - `cosTheta`: 输出锥体半角的余弦值

#### `boundBoxSubtendedConeAngleCenterConstrained`
- **功能**: 带约束的包围锥计算，限制锥体方向在指定方向的半球内

### 曲线几何

#### `sampleSweepSphere`
```slang
void sampleSweepSphere(
    const float4 sphereA,
    const float4 sphereB,
    const float2 uv,
    out float3 hitPos,
    out float3 sphereCenter
)
```
- **功能**: 在两个球体扫掠形成的胶囊体表面上采样点
- **参数**:
  - `sphereA`, `sphereB`: 球体位置和半径 (xyz=位置, w=半径)
  - `uv`: 采样参数 (u∈[0,1]沿曲线, v∈[0,1]绕曲线)
  - `hitPos`: 输出采样点位置
  - `sphereCenter`: 输出对应球心位置

#### `transformSphere`
```slang
float4 transformSphere(const float4 sphere, const float4x4 worldMat)
```
- **功能**: 变换曲线端点的球体
- **参数**:
  - `sphere`: 球体（xyz=位置, w=半径）
  - `worldMat`: 局部到世界的变换矩阵
- **返回**: 变换后的球体

#### `extractScaleFromTransform`
- **功能**: 从4x4变换矩阵中提取各向同性缩放因子

## 依赖关系

### 本文件引用
- `Utils/Math/MathConstants.slangh`
- `Utils.Math.MathHelpers`

### 被以下文件引用
- 场景几何处理模块
- 光线追踪着色器
- 曲线渲染系统

## 实现细节

### 射线原点偏移算法
使用浮点数表示的整数偏移和固定偏移相结合的方法，根据距离原点的远近选择合适的偏移策略，有效避免数值精度问题导致的自相交。

### 包围锥优化
利用AABB关于中心方向平面的对称性，通过优化算法减少计算量，支持约束条件下的锥体计算。

### 曲线采样
支持变半径的胶囊体表面均匀采样，处理端帽和线性段的不同情况，确保采样的正确性和连续性。
