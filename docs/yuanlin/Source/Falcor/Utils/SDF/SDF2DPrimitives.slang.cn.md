# SDF2DPrimitives.slang 着色器文档

> 路径: `Source/Falcor/Utils/SDF/SDF2DPrimitives.slang`
> 类型: Slang 着色器文件
> 模块: Utils/SDF

## 功能概述

定义了一系列 2D 有符号距离场（SDF）图元函数和对应的结构体封装。提供了圆形、正方形、菱形、心形、V 形、环形、标签、十字、星号、无穷符号、图钉、箭头、圆角矩形、三角形、线段、圆角线段和向量箭头等 2D 图元的有符号距离计算。所有图元结构体实现 `ISDF2D` 接口以支持泛型绘制。部分代码基于 JCGT 文章 "Antialiased 2D Grid, Marker, and Arrow Shaders"（Rougier），并进行了修正和扩展。

## 结构体与接口

### `ISDF2D`（接口）

- **职责**: 定义 2D SDF 图元的统一求值接口。

| 方法 | 说明 |
|------|------|
| `float eval(float2 p)` | 在给定 2D 点处计算有符号距离值。负值表示点在图元内部，正值表示在外部 |

### 图元结构体

| 结构体 | 参数 | 说明 |
|--------|------|------|
| `SDF2DCircle` | `radius` | 圆形，支持自定义半径（默认 1.0） |
| `SDF2DSquare` | 无 | 单位正方形（边长 1.0） |
| `SDF2DDiamond` | 无 | 菱形 |
| `SDF2DHeart` | 无 | 心形 |
| `SDF2DChevron` | 无 | V 形 |
| `SDF2DRing` | 无 | 环形（外半径 0.5，内半径 0.25） |
| `SDF2DTag` | 无 | 标签形状 |
| `SDF2DCross` | 无 | 十字形 |
| `SDF2DAsterisk` | 无 | 星号形状 |
| `SDF2DInfinity` | 无 | 无穷符号 |
| `SDF2DPin` | 无 | 图钉形状 |
| `SDF2DArrow` | 无 | 箭头形状 |
| `SDF2DRoundedBox` | `halfSides`, `radius` | 圆角矩形，指定半边长和圆角半径 |
| `SDF2DVector` | `startPoint`, `endPoint`, `width`, `arrowHeight` | 向量箭头，从起点到终点，指定线宽和箭头高度 |
| `SDF2DRoundedLine` | `startPoint`, `endPoint`, `width` | 圆角线段，从起点到终点，指定线宽 |
| `SDF2DTriangle` | `p0`, `p1`, `p2` | 任意三角形（顶点需按逆时针顺序） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float sdfCircle(float2 p, float radius)` | 计算到以原点为圆心、指定半径的圆的有符号距离 |
| `float sdfSquare(float2 p)` | 计算到单位正方形的有符号距离 |
| `float sdfDiamond(float2 p)` | 计算到菱形的有符号距离 |
| `float sdfHeart(float2 p)` | 计算到心形的有符号距离 |
| `float sdfChevron(float2 p)` | 计算到 V 形的有符号距离 |
| `float sdfRing(float2 p)` | 计算到环形的有符号距离 |
| `float sdfTag(float2 p)` | 计算到标签的有符号距离 |
| `float sdfCross(float2 p)` | 计算到十字的有符号距离 |
| `float sdfAsterisk(float2 p)` | 计算到星号的有符号距离 |
| `float sdfInfinity(float2 p)` | 计算到无穷符号的有符号距离 |
| `float sdfPin(float2 p)` | 计算到图钉的有符号距离 |
| `float sdfArrow(float2 p)` | 计算到箭头的有符号距离 |
| `float sdfRoundedBox(float2 p, float2 halfBoxSides, float radius)` | 计算到圆角矩形的有符号距离 |
| `float sdfTriangle(float2 p, float2 p0, float2 p1, float2 p2)` | 计算到任意三角形的有符号距离。顶点需按逆时针顺序排列 |
| `float sdfSegmentDistance(float2 p, float2 p1, float2 p2)` | 计算到线段的有符号距离 |
| `float sdfRoundedLine(float2 p, float2 p1, float2 p2, float width)` | 计算到圆角线段的有符号距离 |
| `float sdfVector(float2 p, float2 p1, float2 p2, float width, float arrowHeight)` | 计算到向量箭头（带箭头的线段）的有符号距离 |

## 依赖关系

### import

- `Utils.Math.MathHelpers` — 数学辅助函数

### include

- `Utils/Math/MathConstants.slangh` — 数学常量（`M_SQRT2` 等）
