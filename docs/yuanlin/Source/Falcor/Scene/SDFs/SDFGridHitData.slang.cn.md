# SDFGridHitData.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFGridHitData.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs

## 功能概述

定义了 `SDFGridHitData` 结构体,用于存储光线与 SDF 网格交叉测试的命中数据。根据不同的 SDF 网格实现,命中数据包含不同的字段。

## 结构体与接口

### `SDFGridHitData`

- **职责**: 存储 SDF 网格交叉测试的命中结果,供后续梯度计算使用

| 字段 | 条件 | 类型 | 说明 |
|------|------|------|------|
| `lod` | NDSDF 实现 | `uint` | 命中时的 LOD 层级 |
| `primitiveID` | SVS 或 SBS 实现 | `uint` | 命中的基元 ID |
| `svoIndex` | SVO 实现 | `uint` | 命中的 SVO 节点索引 |
| `hitT` | 所有实现 | `float` | 命中距离 t 值 |

## 依赖关系

### include
- `Scene/SDFs/SDFGridNoDefines.slangh`
