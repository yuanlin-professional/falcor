# SimpleBTDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/SimpleBTDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现最简单的无折射 Dirac 透射 BTDF。出射方向直接翻转入射方向，可用颜色进行着色。

## 结构体与接口

### `SimpleBTDF`

- **实现**: `IBSDF`
- **叶瓣类型**: `DeltaTransmission`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `transmittance` | `float3` | 透射着色颜色 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
