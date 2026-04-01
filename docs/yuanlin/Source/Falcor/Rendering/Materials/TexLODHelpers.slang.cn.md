# TexLODHelpers.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/TexLODHelpers.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

提供纹理细节层次（LOD）系统的辅助函数。支持光线锥（Ray Cones）、光线微分（Ray Differentials）等纹理 LOD 计算方法。包含光线锥的传播、表面散布计算、纹理 LOD 推导等核心功能。

## 结构体与接口

### `RayCone`

- **职责**: 光线锥的传播和散布计算

| 函数 | 说明 |
|------|------|
| `propagate(surfaceSpreadAngle, hitT)` | 传播光线锥到下一个交点 |
| `computeLOD(txGrad, faceN, ...)` | 从光线锥计算纹理 LOD |

### `RayDiff`

- **职责**: 光线微分的传播

| 函数 | 说明 |
|------|------|
| `propagate(hitPt, hitN, hitT, rayDir)` | 通过反射/折射传播光线微分 |

### 辅助函数

| 函数 | 说明 |
|------|------|
| `computeScreenSpaceSurfaceSpreadAngle(...)` | 从 G 缓冲区计算屏幕空间表面散布角 |
| `computeRayConeTriangleLODValue(...)` | 计算三角形的 LOD 值 |
| `computeAnisotropicEllipseAxes(...)` | 计算各向异性椭圆轴 |

## 依赖关系

### import
- 多个 Utils 和 Scene 模块
