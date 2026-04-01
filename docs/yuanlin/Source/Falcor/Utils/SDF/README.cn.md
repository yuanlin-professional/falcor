# Utils/SDF -- 有符号距离场工具模块

## 功能概述

本模块提供 Falcor 渲染框架中用于 GPU 着色器的有符号距离场 (Signed Distance Field, SDF) 工具集，全部以 Slang 着色器实现，涵盖：

- **2D SDF 图元** -- 圆、正方形、菱形、心形、箭头、圆角矩形、三角形、线段等 17 种 2D 图元的有符号距离函数，以及面向对象的 `ISDF2D` 接口和对应结构体（`SDF2DPrimitives`）。
- **2D SDF 绘制** -- 变换（缩放/旋转/平移）、填充与抗锯齿混合的绘制工具函数（`SDF2DDraw`）。
- **3D SDF 形状** -- 球体、椭球、长方体、环面、圆锥、胶囊体、包围盒等 7 种 3D 图元，每种均提供标量版和区间算术版（`SDF3DShapes`）。
- **SDF 运算** -- 并集、交集、减集及对应的平滑版本，支持标量和区间算术两种模式（`SDFOperations`）。
- **运算类型枚举** -- 共享的 `SDFOperationType` 枚举（`SDFOperationType`）。

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `SDF2DPrimitives.slang` | Slang 着色器 | 2D SDF 图元函数与 `ISDF2D` 接口（圆、方、心、箭头等） |
| `SDF2DDraw.slang` | Slang 着色器 | 2D SDF 变换、填充、抗锯齿绘制函数 |
| `SDF3DShapes.slang` | Slang 着色器 | 3D SDF 图元（球、椭球、盒、环、锥、胶囊、包围盒）及区间版本 |
| `SDFOperations.slang` | Slang 着色器 | SDF 布尔运算（并/交/减）及平滑版本，含区间算术 |
| `SDFOperationType.slang` | Slang 头文件 | `SDFOperationType` 枚举定义（Union/Subtraction/Intersection 等） |

## 依赖关系

| 依赖项 | 用途 |
|--------|------|
| `Utils/Math/MathConstants.slangh` | 数学常量（`M_SQRT2` 等） |
| `Utils/Math/MathHelpers` | 矩阵求逆等数学辅助函数 |
| `Utils/Math/IntervalArithmetic` | 区间算术运算（`ivlAdd`, `ivlMul`, `ivlLength` 等） |
| `Utils/HostDeviceShared.slangh` | 主机/设备共享宏（`BEGIN_NAMESPACE_FALCOR`） |

## 关键类与接口

### `ISDF2D` (Slang 接口)
2D SDF 图元的统一接口。

- `eval(float2 p) -> float` -- 计算点 p 到图元的有符号距离

实现该接口的结构体：
`SDF2DCircle`, `SDF2DSquare`, `SDF2DDiamond`, `SDF2DHeart`, `SDF2DChevron`, `SDF2DRing`, `SDF2DTag`, `SDF2DCross`, `SDF2DAsterisk`, `SDF2DInfinity`, `SDF2DPin`, `SDF2DArrow`, `SDF2DRoundedBox`, `SDF2DVector`, `SDF2DRoundedLine`, `SDF2DTriangle`

### 2D 绘制函数 (SDF2DDraw.slang)
- `sdfComputeTransform(scale, rotationAngle, translation)` -- 计算逆变换矩阵 (2x3)
- `sdfTransform(p, scale, rotationAngle, translation)` -- 对点施加变换
- `sdfFilledAlpha(signedDistance, lineWidth, antialiasWidth)` -- 计算填充 alpha（抗锯齿）
- `sdfFilled(signedDistance, lineWidth, antialiasWidth, fillColor)` -- 计算带 alpha 的填充颜色
- `sdfDraw(sdf, p, scale, rotationAngle, translation, primitiveColor, backgroundColor, blendRadius)` -- 完整绘制流程

### 3D SDF 图元函数 (SDF3DShapes.slang)
每种图元提供标量版和区间算术版（前缀 `sdfInterval`）：

| 函数 | 图元 |
|------|------|
| `sdfSphere(p, r)` | 球体 |
| `sdfEllipsoid(p, r)` | 椭球 |
| `sdfBox(p, b)` | 长方体 |
| `sdfTorus(p, r)` | 环面 |
| `sdfCone(p, tan, h)` | 圆锥 |
| `sdfCapsule(p, hl)` | 胶囊体 |
| `sdfBoundingBox(point, halfBoxSides, edgeThickness)` | 包围盒线框 |

### SDF 布尔运算 (SDFOperations.slang)
标准运算与平滑运算，均支持标量和区间两种模式：

| 标准运算 | 平滑运算 | 说明 |
|----------|----------|------|
| `sdfUnion(a, b)` | `sdfSmoothUnion(a, b, k)` | 并集 |
| `sdfIntersection(a, b)` | `sdfSmoothIntersection(a, b, k)` | 交集 |
| `sdfSubtraction(a, b)` | `sdfSmoothSubtraction(a, b, k)` | 减集 |

- `sdfBlend(d1, d2, radius)` -- 基于半径的融合混合（RTR4 公式 17.57）
- 平滑运算基于 Inigo Quilez 的 `smin` / `smax` 算法

### `SDFOperationType` 枚举
```
Union, Subtraction, Intersection, SmoothUnion, SmoothSubtraction, SmoothIntersection
```
