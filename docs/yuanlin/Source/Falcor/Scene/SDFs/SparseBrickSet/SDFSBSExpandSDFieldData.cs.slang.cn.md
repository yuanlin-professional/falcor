# SDFSBSExpandSDFieldData.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSExpandSDFieldData.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,用于扩展 SDF 距离场数据到更大的纹理。当网格分辨率因编辑操作需要增加时,此着色器将旧的距离场数据复制到新纹理的正确位置,并应用扩展因子进行距离值缩放。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gSDFieldData.gridWidthInValues` | `uint` | 源网格的值宽度 |
| `gSDFieldData.offset` | `uint` | 在目标纹理中的偏移 |
| `gSDFieldData.expansionFactor` | `float` | 距离值扩展因子 |
| `gSDFieldData.oldSDField` | `Texture3D<float>` | 源距离场纹理 |
| `gSDFieldData.newSDField` | `RWTexture3D<float>` | 目标距离场纹理 |

## 依赖关系

### include
- `Utils/Math/MathConstants.slangh`

## 实现细节

- 读取旧纹理坐标 `dispatchThreadID`,写入新纹理坐标 `dispatchThreadID + offset`
- 距离值乘以 `expansionFactor` 以适应新网格分辨率
