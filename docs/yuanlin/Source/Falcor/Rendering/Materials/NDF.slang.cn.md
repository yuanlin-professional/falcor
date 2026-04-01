# NDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/NDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现两种具体的法线分布函数（NDF）：Trowbridge-Reitz（GGX）和 Beckmann-Spizzichino。两者均实现 `INDF` 接口，支持椭圆各向异性和可见法线采样。

## 结构体与接口

### `TrowbridgeReitzNDF`

- **实现**: `INDF`
- **职责**: GGX/Trowbridge-Reitz 法线分布函数
- **采样**: 使用 Heitz & d'Eon 2014 的可见法线采样（球面投影方法）

### `BeckmannSpizzichinoNDF`

- **实现**: `INDF`
- **职责**: Beckmann-Spizzichino 法线分布函数
- **采样**: 使用 Eric Heitz 2020 三角切割参数化（triangle-cut parameterization）的解析采样方案，对陡峭上行射线使用 Eugene d'Eon 的级数展开稳定化

## 实现细节

- Beckmann NDF 的 `evalSigmaStd` 对极端角度使用 Eugene d'Eon 推导的级数展开以保持数值稳定
- 可见斜率采样包含完整的 Beckmann 解析反演方案，替代了传统的数值牛顿求解

## 依赖关系

### import / export
- `Rendering.Materials.INDF`（导出）
- `Utils.Math.MathHelpers`
