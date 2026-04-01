# SDF3DShapes.slang 着色器文档

> 路径: `Source/Falcor/Utils/SDF/SDF3DShapes.slang`
> 类型: Slang 着色器文件
> 模块: Utils/SDF

## 功能概述

定义了多种 3D 有符号距离场（SDF）图元的距离计算函数，包括球体、椭球体、长方体、圆环、圆锥、胶囊体和包围盒。每种图元提供两个版本：标准版本（输入单点，返回标量距离）和区间算术版本（输入 AABB 包围盒的最小/最大角点，返回距离区间 `float2`）。区间算术版本用于加速空间查询（如光线步进中的安全步长计算）。

## 函数

### 球体（Sphere）

| 函数签名 | 说明 |
|----------|------|
| `float sdfSphere(float3 p, float r)` | 计算点 `p` 到以原点为中心、半径为 `r` 的球体的有符号距离 |
| `float2 sdfIntervalSphere(float3 pMin, float3 pMax, float r)` | 使用区间算术计算 AABB 包围盒到球体的距离区间 |

### 椭球体（Ellipsoid）

| 函数签名 | 说明 |
|----------|------|
| `float sdfEllipsoid(float3 p, float3 r)` | 计算点到以原点为中心、三轴半径为 `r` 的椭球体的近似有符号距离 |
| `float2 sdfIntervalEllipsoid(float3 pMin, float3 pMax, float3 r)` | 使用区间算术计算椭球体的距离区间 |

### 长方体（Box）

| 函数签名 | 说明 |
|----------|------|
| `float sdfBox(float3 p, float3 b)` | 计算点到以原点为中心、半边长为 `b` 的长方体的有符号距离 |
| `float2 sdfIntervalBox(float3 pMin, float3 pMax, float3 b)` | 使用区间算术计算长方体的距离区间 |

### 圆环（Torus）

| 函数签名 | 说明 |
|----------|------|
| `float sdfTorus(float3 p, float r)` | 计算点到以原点为中心、主半径为 `r` 的圆环的有符号距离。管半径为零，可通过 `shapeBlobbing` 添加 |
| `float2 sdfIntervalTorus(float3 pMin, float3 pMax, float r)` | 使用区间算术计算圆环的距离区间 |

### 圆锥（Cone）

| 函数签名 | 说明 |
|----------|------|
| `float sdfCone(float3 p, float tan, float h)` | 计算点到圆锥的有符号距离。圆锥顶点在 `(0, h, 0)`，底面在 xz 平面。`tan` 为半顶角正切值，底面半径为 `tan * h` |
| `float2 sdfIntervalCone(float3 pMin, float3 pMax, float tan, float h)` | 使用区间算术计算圆锥的距离区间 |

### 胶囊体（Capsule）

| 函数签名 | 说明 |
|----------|------|
| `float sdfCapsule(float3 p, float hl)` | 计算点到以原点为中心、沿 Y 轴从 `-hl` 到 `+hl` 的胶囊体中轴线的有符号距离。半径为零，可通过 `shapeBlobbing` 添加 |
| `float2 sdfIntervalCapsule(float3 pMin, float3 pMax, float hl)` | 使用区间算术计算胶囊体的距离区间 |

### 包围盒（Bounding Box）

| 函数签名 | 说明 |
|----------|------|
| `float sdfBoundingBox(float3 point, float3 halfBoxSides, float edgeThickness)` | 计算点到包围盒边框的有符号距离。只渲染边框线条，不渲染面。`edgeThickness` 控制边框粗细 |

## 依赖关系

### import

- `Utils.Math.IntervalArithmetic` — 区间算术运算库（`ivlLength`、`ivlSub`、`ivlAdd`、`ivlDiv`、`ivlMul`、`ivlAbs`、`ivlMax`、`ivlMin`、`ivlSaturate`、`ivlClamp`、`ivlSquare`、`ivlSqrt` 等）
