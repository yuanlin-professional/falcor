# HitInfoType.slang 源码文档

> 路径: `Source/Falcor/Scene/HitInfoType.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene

## 功能概述

定义光线追踪命中类型的枚举 `HitType`，在 CPU 和 GPU 之间共享。此文件是 HitInfo 系统的基础类型定义，被 `HitInfo.slang` 和 `HitInfo.cpp` 同时引用。

## 结构体与接口

### `enum class HitType : uint32_t`

| 枚举值 | 数值 | 说明 |
|--------|------|------|
| `None` | 0 | 无命中 |
| `Triangle` | 1 | 三角形命中 |
| `Volume` | 2 | 体积命中 |
| `DisplacedTriangle` | 3 | 位移三角形命中（仅非压缩模式） |
| `Curve` | 4 | 曲线命中（仅非压缩模式） |
| `SDFGrid` | 6 | SDF 网格命中（仅非压缩模式） |
| `Count` | - | 枚举计数，必须为最后一项 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` - 主机/设备共享宏（`BEGIN_NAMESPACE_FALCOR` 等）
