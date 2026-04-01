# BeerBTDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/BeerBTDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现基于 Beer 定律的无折射 Dirac 透射 BTDF。吸收颜色由 Beer 定律决定，吸收系数与厚度已合并。提供路径缩短（path shortening）参数控制掠射角饱和。

## 结构体与接口

### `BeerBTDF`

- **实现**: `IBSDF`
- **职责**: Delta 透射，颜色由指数衰减决定

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `absorption` | `float3` | 吸收系数乘以厚度 |
| `pathshortening` | `float` | 掠射角饱和控制，通常由 IOR 计算 |

#### 叶瓣类型
- `DeltaTransmission`

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
