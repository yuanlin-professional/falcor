# Utils/Geometry -- 几何工具库

## 功能概述

`Utils/Geometry` 提供 Falcor 渲染框架中 GPU 端的几何计算工具函数，全部以 Slang 着色器形式实现。该模块覆盖了光线追踪、三角形处理、曲线参数化和各类射线-几何体求交测试。

主要功能包括：

- **射线原点偏移**：基于 Ray Tracing Gems 第 6 章的方法（Wachter & Binder），通过整数位操作精确偏移射线原点以避免自交。支持前向/反向可微分。
- **包围锥计算**：从视点计算 AABB 的最小包围锥角度（基于中心法和平均法两种算法），以及球体的包围锥角度。
- **三角形工具**：计算点到三角形的最小距离平方、三角形裁剪到 2D 矩形的面积、射线到三角形的距离、重心坐标计算。
- **2D 多边形裁剪**：通用轴对齐平面 2D 多边形裁剪（Sutherland-Hodgman 风格）。
- **曲线 UV 参数化**：基于线性扫球（Linear Swept Sphere）的曲线表面 UV 参数化，包括正反向转换。
- **射线求交测试**：射线-球体、射线-AABB、射线-三角形（防水方法）、射线-线性扫球等标准求交算法。
- **变换工具**：从变换矩阵提取各向同性缩放因子、球体变换。

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `GeometryHelpers.slang` | Slang 着色器 | 射线原点偏移（可微分）、包围锥计算、三角形工具（距离/面积/重心坐标）、2D 多边形裁剪、曲线 UV 参数化、变换提取 |
| `IntersectionHelpers.slang` | Slang 着色器 | 射线-球体求交、射线-AABB 求交、射线-三角形防水求交、射线-线性扫球求交 |

## 依赖关系

### 外部依赖
- `Utils/Math/MathConstants.slangh` -- 数学常量（PI、FLT_MAX 等）
- `Utils/Math/MathHelpers` (Slang import) -- 数学辅助函数（`perp_stark` 正交向量生成等）

### 被依赖（下游模块）
- `Scene/` -- 场景光线追踪使用射线原点偏移和求交测试
- `RenderPasses/` -- 渲染通道使用各类几何计算
- `Rendering/` -- 光照和阴影计算使用包围锥和求交

## 关键类与接口

### GeometryHelpers.slang

#### 射线原点计算
```slang
float3 computeRayOrigin(float3 pos, float3 normal)
```
基于三角形命中位置和面法线计算新射线原点，通过对浮点数的整数位表示进行偏移来精确避免自交。提供前向导数 (`fwd_computeRayOrigin`) 和反向导数 (`bwd_computeRayOrigin`) 支持可微分渲染。

#### 包围锥
```slang
void boundBoxSubtendedConeAngleCenter(float3 origin, float3 aabbMin, float3 aabbMax,
                                       out float3 coneDir, out float sinTheta, out float cosTheta)
void boundBoxSubtendedConeAngleAverage(float3 origin, float3 aabbMin, float3 aabbMax,
                                        out float3 coneDir, out float sinTheta, out float cosTheta)
void boundSphereSubtendedConeAngle(float3 center, float sqrRadius, out float sinTheta, out float cosTheta)
```
- 中心法利用 AABB 对称性优化，测试 4 个角点
- 平均法计算所有 8 个角点方向的平均值再求最大偏角

#### 三角形工具
```slang
float computeSquaredMinDistanceToTriangle(const float3 vertices[3], const float3 p)
float computeClippedTriangleArea2D(const float2 pos[3], const float2 minPoint, const float2 maxPoint)
float computeRayDistanceToTriangle(const float3 v[3], const float3 origin, const float3 direction)  // 可微分
void computeBarycentrics(const float3 v[3], const float3 p, out float3 barycentrics)
```

#### 曲线参数化
```slang
float2 curveHitPosToUV(const float3 hitPos, const float4 sphereA, const float4 sphereB)
void uvToCurveHitPos(const float2 uv, const float4 sphereA, const float4 sphereB,
                     out float3 hitPos, out float3 sphereCenter)
```
基于线性扫球模型（Han et al. 2019），在射线-曲线命中位置与 UV 参数之间双向转换。

### IntersectionHelpers.slang

#### 射线-球体求交
```slang
bool intersectRaySphere(float3 rayOrigin, float3 rayDir, float3 sphereCenter, float sphereRadius,
                        out float t, float tMin = 0, float tMax = 1e36, bool backfaceCull = false)
```
基于 Ray Tracing Gems 第 7 章的高精度解析方法，返回最近交点距离。

#### 射线-AABB 求交
```slang
bool intersectRayAABB(float3 rayOrigin, float3 rayDir, float3 aabbMin, float3 aabbMax, out float2 nearFar)
```
标准 slab 方法，返回进入/离开参数区间。

#### 射线-三角形求交（防水方法）
```slang
bool intersectRayTriangle(float3 rayOrigin, float3 rayDir, const float3 vertices[3],
                          out float3 barycentrics, out float t)
```
基于 Wachter & Binder 的防水射线-三角形求交算法（JCGT 2013），通过坐标轴投影和双精度回退避免数值精度问题。

#### 射线-线性扫球求交
```slang
bool intersectLinearSweptSphereHan19(float3 rayOrigin, float3 rayDir, float4 sphereA, float4 sphereB,
                                      bool useSphereJoints, out float2 result)
```
基于 Han et al. 2019 论文，实现射线与线性扫球（管状曲线基元）的求交测试，返回最近交点距离和线性插值参数。
