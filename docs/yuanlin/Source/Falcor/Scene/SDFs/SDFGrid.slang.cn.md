# SDFGrid.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFGrid.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs

## 功能概述

`SDFGrid.slang` 是有符号距离场网格的着色器入口文件,根据编译时宏定义 `SCENE_SDF_GRID_IMPLEMENTATION` 选择具体的 SDF 网格实现,并将其类型别名为统一的 `SDFGrid` 类型。这使得上层着色器代码可以通过统一接口访问不同的 SDF 网格实现。

## 结构体与接口

### `SDFGridDummy`

- **继承**: `SDFGridBase`, `ISDFGrid`
- **职责**: 当场景中不存在 SDF 时提供的空实现,确保编译不会失败

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `bool intersectSDF(...)` | 始终返回 `false`,不执行任何交叉测试 |
| `bool intersectSDFAny(...)` | 始终返回 `false` |
| `void decodeHit(...)` | 输出零梯度 |

## 条件编译逻辑

| 宏定义值 | 选择的实现 | 类型别名 |
|----------|-----------|---------|
| `SCENE_SDF_GRID_IMPLEMENTATION_NDSDF` | `NDSDFGrid` | `SDFGrid` |
| `SCENE_SDF_GRID_IMPLEMENTATION_SVS` | `SDFSVS` | `SDFGrid` |
| `SCENE_SDF_GRID_IMPLEMENTATION_SBS` | `SDFSBS` | `SDFGrid` |
| `SCENE_SDF_GRID_IMPLEMENTATION_SVO` | `SDFSVO` | `SDFGrid` |
| 其他 | `SDFGridDummy` | `SDFGrid` |

## 依赖关系

### import
- `Scene.SDFs.SDFGridHitData` (导出)
- `Scene.SDFs.NormalizedDenseSDFGrid.NDSDFGrid`
- `Scene.SDFs.SparseVoxelSet.SDFSVS`
- `Scene.SDFs.SparseBrickSet.SDFSBS`
- `Scene.SDFs.SparseVoxelOctree.SDFSVO`
- `Scene.SDFs.SDFGridBase`

### include
- `Scene/SDFs/SDFGridNoDefines.slangh`
