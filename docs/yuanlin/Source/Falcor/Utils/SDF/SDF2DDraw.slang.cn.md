# SDF2DDraw.slang 着色器文档

> 路径: `Source/Falcor/Utils/SDF/SDF2DDraw.slang`
> 类型: Slang 着色器文件
> 模块: Utils/SDF

## 功能概述

提供 2D 有符号距离场（SDF）图元的变换、填充和绘制功能。该文件包含计算变换矩阵、应用变换到查询点、基于有符号距离进行抗锯齿填充、以及将 SDF 图元与背景颜色混合绘制的完整管线。基于 JCGT 文章 "Antialiased 2D Grid, Marker, and Arrow Shaders"（Rougier）的抗锯齿方法。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float2x3 sdfComputeTransform(float2 scale, float rotationAngle, float2 translation)` | 计算用于缩放、旋转和平移 SDF 图元的逆 2x3 变换矩阵。变换顺序为：先缩放，后旋转，最后平移。返回逆矩阵，可直接应用于查询点 |
| `float2 sdfTransform(float2 p, float2 scale, float rotationAngle, float2 translation)` | 对 2D 点应用缩放、旋转和平移变换。内部调用 `sdfComputeTransform` 获取逆矩阵 |
| `float sdfFilledAlpha(float signedDistance, float lineWidth, float antialiasWidth)` | 根据有符号距离计算填充 alpha 值。内部为负时返回 1.0（完全填充），边界区域使用 `smoothstep` 进行平滑过渡，外部返回 0.0 |
| `float4 sdfFilled(float signedDistance, float lineWidth, float antialiasWidth, float4 fillColor)` | 根据有符号距离返回带 alpha 的填充颜色。alpha 值通过 `sdfFilledAlpha` 计算 |
| `float4 sdfDraw<SDF : ISDF2D>(const SDF sdf, float2 p, float2 scale, float rotationAngle, float2 translation, float4 primitiveColor, float4 backgroundColor, float blendRadius)` | 绘制函数（带变换）。先对查询点应用变换，然后求值 SDF，计算填充颜色并与背景颜色混合。`blendRadius` 控制抗锯齿区域宽度 |
| `float4 sdfDraw<SDF : ISDF2D>(const SDF sdf, float2 p, float4 primitiveColor, float4 backgroundColor, float blendRadius)` | 绘制函数（无变换）。使用默认单位缩放、零旋转、零平移调用带变换版本 |

## 结构体与接口

本文件使用 `ISDF2D` 接口（定义于 `SDF2DPrimitives.slang`）作为泛型约束，允许 `sdfDraw` 函数接受任意实现该接口的 SDF 图元类型。

## 依赖关系

### import

- `Utils.SDF.SDF2DPrimitives` — 2D 有符号距离场图元定义和 `ISDF2D` 接口
- `Utils.Math.MathHelpers` — 数学辅助函数（`inverse` 矩阵求逆）

### include

- `Utils/Math/MathConstants.slangh` — 数学常量
