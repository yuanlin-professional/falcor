# RGLCommon.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/RGLCommon.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

提供 RGL 材质系统的公共工具集，包括球面坐标转换、坐标扭曲（CoordinateWarp）、2D/4D 数据插值（Brick2D/Brick4D），以及可插值 2D 概率分布的采样/反转/PDF 求值（InterpolatedDistribution2D）。

## 结构体与接口

### `CoordinateWarp`

- **职责**: 1D 坐标表定义的逆坐标扭曲

### `Brick2D`

- **职责**: 2D 浮点数组的双线性插值

### `Brick4D`

- **职责**: 4D RGB 浮点数组的多线性插值（RGL 数据集专用布局）

### `InterpolatedDistribution2D`

- **职责**: 可在两个维度上插值的 2D PDF 采样器，支持采样、反转和 PDF 求值
- 使用分段线性 PDF 和二次 CDF 的精确求逆

## 辅助函数

| 函数 | 说明 |
|------|------|
| `toSpherical(w)` / `fromSpherical(s)` | 方向与球面坐标互转 |
| `sphericalToUnit(angles)` / `unitToSpherical(uv)` | 球面坐标与单位正方形互转 |
| `packSize` / `unpackSize` | 4D 尺寸的压缩/解压 |

## 依赖关系

### import
- `Utils.Math.MathHelpers`
- `Scene.Scene`
