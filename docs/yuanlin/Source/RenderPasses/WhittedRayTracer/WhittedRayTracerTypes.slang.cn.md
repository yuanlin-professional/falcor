# WhittedRayTracerTypes.slang 源码文档

> 路径: `Source/RenderPasses/WhittedRayTracer/WhittedRayTracerTypes.slang`
> 类型: Slang 着色器头文件
> 模块: RenderPasses/WhittedRayTracer

## 功能概述

定义 WhittedRayTracer 使用的共享枚举类型。该文件在 CPU 和 GPU 之间共享，定义了光线足迹过滤模式的枚举。

## 结构体与接口

### `RayFootprintFilterMode`

光线足迹过滤模式枚举（CPU/GPU 共享），用于控制光线锥和光线微分的过滤行为。

| 枚举值 | 数值 | 说明 |
|--------|------|------|
| `Isotropic` | 0 | 各向同性过滤 |
| `Anisotropic` | 1 | 各向异性过滤 |
| `AnisotropicWhenRefraction` | 2 | 仅在折射时使用各向异性过滤 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享工具宏（`BEGIN_NAMESPACE_FALCOR` 等）

## 实现细节

- 使用 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 宏注册枚举的字符串映射，支持 UI 下拉框和脚本序列化。
- 该枚举定义在 Falcor 命名空间内，确保 CPU/GPU 端的内存布局一致（使用 `uint32_t` 底层类型）。
