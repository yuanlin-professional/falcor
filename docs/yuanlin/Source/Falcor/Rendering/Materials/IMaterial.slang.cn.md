# IMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/IMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

定义材质接口 `IMaterial` 和基类 `MaterialBase`。材质实现持有描述材质属性的所有数据，通过 `MaterialSystem` 创建和初始化。查询着色点时返回实现 `IMaterialInstance` 接口的材质实例（模式生成过程）。

## 结构体与接口

### `IMaterial`（接口）

- **anyValueSize**: 128 字节（对应 `MaterialDataBlob` 大小）
- **关联类型**: `MaterialInstance : IMaterialInstance`

| 方法签名 | 说明 |
|----------|------|
| `setupMaterialInstance(ms, sd, lod, hints)` | 在着色点创建材质实例（执行模式生成） |
| `setupDiffMaterialInstance(diffData, ms, sd, lod, hints)` | 可微分版本，额外输出 `DiffMaterialData` |
| `getHomogeneousVolumeProperties(ms, materialID)` | 获取均匀体积属性 |

### `MaterialBase`（开放结构体）

- **职责**: 提供 `IMaterial` 的默认实现

| 成员 | 类型 | 说明 |
|------|------|------|
| `header` | `MaterialHeader` | 材质头信息 |

## 依赖关系

### export
- `Rendering.Materials.IMaterialInstance`
- `Scene.Material.MaterialSystem`
- `Scene.Material.TextureSampler`
- `Scene.Material.VolumeProperties`
- `Scene.ShadingData`

### import
- `Rendering.Volumes.PhaseFunction`
