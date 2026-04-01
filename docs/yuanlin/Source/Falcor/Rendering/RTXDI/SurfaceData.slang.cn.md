# SurfaceData.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/SurfaceData.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/RTXDI

## 功能概述

本文件定义了 RTXDI 中用于存储主命中点表面信息的 `SurfaceData` 结构体。它是 `PackedSurfaceData` 的解包高级表示，通过 Slang 属性（property）提供紧凑位域的透明访问。在 RTXDI 的应用桥接中，`SurfaceData` 被别名为 `RAB_Surface`。

## 结构体与接口

### `SurfaceData`

| 成员/属性 | 类型 | 说明 |
|----------|------|------|
| `position` | `float3` | 世界空间位置 |
| `viewDepth` | `float` | 观察深度（负值表示无效表面） |
| `packedNormal` | `uint` | 打包法线（八面体映射 2x16） |
| `packedWeights` | `uint` | 打包权重（4 个 8 位 UNORM） |
| `viewDir` | `float3` | 观察方向（不存储在打包数据中） |
| `valid` | `bool` (property) | 表面是否有效 (`viewDepth >= 0`) |
| `normal` | `float3` (property) | 着色法线（读写） |
| `diffuse` | `float` (property) | 漫反射反射率（UNORM 8 位, byte 0） |
| `specular` | `float` (property) | 高光反射率（UNORM 8 位, byte 1） |
| `roughness` | `float` (property) | 高光粗糙度（UNORM 8 位, byte 2） |
| `diffuseProb` | `float` (property) | 漫反射波瓣概率（UNORM 8 位, byte 3） |

## 函数

| 函数 | 说明 |
|------|------|
| `__init()` | 构造无效表面（viewDepth = -1） |
| `__init(packed, viewDir)` | 从打包数据和观察方向构造表面 |
| `pack() -> PackedSurfaceData` | 打包为 `PackedSurfaceData` |

## 依赖关系 / import

- `Utils.Math.PackedFormats`（`encodeNormal2x16`/`decodeNormal2x16`）
- `Utils.Math.FormatConversion`（`packUnorm8`/`unpackUnorm8`）
- `PackedTypes`（`__exported import`，导出 `PackedSurfaceData`）

## 实现细节

- 法线使用八面体映射编码为 2 个 16 位整数，提供均匀的精度分布
- 四个材质属性各占 `packedWeights` 的一个字节（8 位 UNORM），通过位操作实现读写
- `viewDir` 不存储在打包数据中，而是在解包时从相机参数和像素位置重建
- 无效表面通过 `viewDepth < 0` 标识，避免额外的布尔标志开销
