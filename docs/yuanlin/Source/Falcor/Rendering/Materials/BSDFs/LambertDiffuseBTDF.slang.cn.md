# LambertDiffuseBTDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/LambertDiffuseBTDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现 Lambert 漫透射 BTDF。出射方向位于下半球，使用余弦加权采样后翻转 z 分量。支持可微分渲染。

## 结构体与接口

### `LambertDiffuseBTDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **叶瓣类型**: `DiffuseTransmission`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 漫反射反照率 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
