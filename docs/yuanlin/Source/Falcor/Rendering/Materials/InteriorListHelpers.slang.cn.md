# InteriorListHelpers.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/InteriorListHelpers.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

提供基于内部介质列表（InteriorList）计算外部折射率的辅助函数。用于确定光线在进入或离开电介质体积时外侧介质的 IOR。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `computeOutsideIoR(interiorList, materialID, entering)` | 计算当前电介质体积外侧的折射率 |

## 依赖关系

### import / export
- `InteriorList`（导出）
- `Scene.Scene`
