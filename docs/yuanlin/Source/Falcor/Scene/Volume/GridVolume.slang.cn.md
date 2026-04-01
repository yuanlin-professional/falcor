# GridVolume.slang 源码文档

> 路径: `Source/Falcor/Scene/Volume/GridVolume.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Volume

## 功能概述

GPU 端的网格体积结构体，提供体积属性的访问接口。包括世界空间包围盒查询、发射模式、各向异性参数以及密度/发射网格的存在性检查。

## 结构体与接口

### `GridVolume`

| 成员 | 类型 | 说明 |
|------|------|------|
| `data` | `GridVolumeData` | 体积参数数据 |

### `GridVolume::EmissionMode` (枚举)

| 值 | 说明 |
|----|------|
| `Direct = 0` | 直接发射模式 |
| `Blackbody = 1` | 黑体辐射模式 |

| 常量 | 说明 |
|------|------|
| `kInvalidGrid = -1` | 无效网格索引标记 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `AABB getBounds()` | 获取世界空间包围盒 |
| `EmissionMode getEmissionMode()` | 获取发射模式 |
| `float getAnisotropy()` | 获取相位函数各向异性参数 |
| `bool hasDensityGrid()` | 检查是否有密度网格 |
| `bool hasEmissionGrid()` | 检查是否有发射网格 |
| `uint getDensityGrid()` | 获取密度网格索引 |
| `uint getEmissionGrid()` | 获取发射网格索引 |

## 依赖关系 / import

- `Utils.Math.AABB` — 包围盒（导出）
- `Grid` — 体素网格（导出）
- `GridVolumeData` — 体积数据结构
