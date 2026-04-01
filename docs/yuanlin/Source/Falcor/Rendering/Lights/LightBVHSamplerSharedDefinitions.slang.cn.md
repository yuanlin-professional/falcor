# LightBVHSamplerSharedDefinitions.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVHSamplerSharedDefinitions.slang`
> 类型: Slang 着色器文件（CPU/GPU 共享）
> 模块: Rendering/Lights

## 功能概述

本文件定义了 LightBVH 采样器在 CPU 和 GPU 之间共享的枚举类型。主要定义了立体角约束方法枚举 `SolidAngleBoundMethod`，控制在 BVH 遍历时如何估算节点对着色点所张的立体角，从而影响 NdotL 的保守上界计算。

## 结构体与接口

### `SolidAngleBoundMethod` (枚举)

| 值 | 数值 | 说明 |
|----|------|------|
| `BoxToCenter` | 1 | 计算到 AABB 中心方向的包围锥，较精确但计算成本中等 |
| `BoxToAverage` | 2 | 计算到所有 AABB 角点平均方向的包围锥，最精确但计算成本最高 |
| `Sphere` | 3 | 使用包围球近似 AABB，最快但最不保守 |

## 实现细节

- 使用 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 支持 CPU/GPU 共享命名空间
- 通过 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 在 CPU 端注册枚举元信息，用于 GUI 下拉框和序列化

## 依赖关系

### import 引用

- `Utils/HostDeviceShared.slangh` — CPU/GPU 共享基础设施
