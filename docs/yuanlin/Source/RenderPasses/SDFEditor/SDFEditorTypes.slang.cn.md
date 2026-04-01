# SDFEditorTypes.slang 源码文档

> 路径: `Source/RenderPasses/SDFEditor/SDFEditorTypes.slang`
> 类型: Slang 共享类型定义
> 模块: RenderPasses/SDFEditor

## 功能概述

定义 SDF 编辑器的核心共享类型，包括网格平面、包围盒渲染设置、编辑轴、拾取信息和编辑数据等。这些类型在 CPU 端（C++）和 GPU 端（着色器）之间共享。

## 结构体与接口

### `SDFGridPlane`

网格平面（用于编辑辅助和对称平面）。

| 成员 | 类型 | 说明 |
|------|------|------|
| `position` | `float3` | 平面位置 |
| `gridLineWidth` | `float` | 网格线宽度 |
| `normal` | `float3` | 平面法线 |
| `gridScale` | `float` | 网格缩放 |
| `rightVector` | `float3` | 右向量 |
| `planeSize` | `float` | 平面显示大小 |
| `color` | `float4` | 平面颜色 |
| `active` | `uint` | 是否激活 |

方法: `float intersect(float3 rayOrigin, float3 rayDir)` — 计算射线到平面的距离。

### `SDFBBRenderMode` (枚举)

包围盒渲染模式：`Disabled`(0)、`RenderAll`(1)、`RenderSelectedOnly`(2)。

### `SDFEditorAxis` (枚举)

编辑轴：`X`(0)、`Y`(1)、`Z`(2)、`OpSmoothing`(3)、`All`(4)。

### `SDFBBRenderSettings`

| 成员 | 类型 | 说明 |
|------|------|------|
| `renderMode` | `uint` | 渲染模式 |
| `selectedInstanceID` | `uint` | 选中实例 ID |
| `edgeThickness` | `float` | 边缘厚度 |

### `SDFPickingInfo`

| 成员 | 类型 | 说明 |
|------|------|------|
| `distance` | `float` | 距离 |
| `instanceID` | `uint` | 实例 ID |
| `hitType` | `HitType` | 命中类型 |

### `SDFEditingData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `editing` | `uint` | 是否正在编辑 |
| `previewEnabled` | `uint` | 是否启用预览 |
| `instanceID` | `uint` | 实例 ID |
| `scalingAxis` | `uint` | 缩放轴 |
| `primitive` | `SDF3DPrimitive` | 当前图元 |
| `symmetryPrimitive` | `SDF3DPrimitive` | 对称图元 |
| `primitiveBB` | `AABB` | 图元包围盒 |
| `symmetryPrimitiveBB` | `AABB` | 对称图元包围盒 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh`
- `Utils/Math/AABB.h` / `Utils.Math.AABB`
- `Scene/HitInfoType.slang` / `Scene.HitInfoType`
- `Scene/SDFs/SDF3DPrimitiveCommon.slang` / `Scene.SDFs.SDF3DPrimitive`
