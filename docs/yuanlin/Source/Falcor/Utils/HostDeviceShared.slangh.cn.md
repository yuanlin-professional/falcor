# HostDeviceShared.slangh 源码文档

> 路径: `Source/Falcor/Utils/HostDeviceShared.slangh`
> 类型: Slang 着色器头文件 (.slangh)
> 模块: Utils

## 功能概述

CPU/GPU 共享代码的粘合层头文件。通过条件编译宏实现同一份数据结构定义在 C++ 主机端和 HLSL/Slang 着色器端都能正确编译和使用。定义了位操作宏、命名空间宏以及类型转换的统一接口。

## 结构体与接口

无独立结构体定义。本文件主要提供宏定义。

## 函数

### CPU 端 (`HOST_CODE` 分支)

通过 `using` 声明将 `math` 命名空间中的函数引入 `Falcor` 命名空间：

| 函数 | 说明 |
|------|------|
| `sign` | 符号函数 |
| `f16tof32` / `f32tof16` | 半精度/单精度浮点转换 |
| `asfloat` / `asfloat16` | 位模式重解释为浮点 |
| `asint` / `asuint` / `asuint16` | 位模式重解释为整数 |

### 通用宏

| 宏 | 说明 |
|----|------|
| `EXTRACT_BITS(bits, offset, value)` | 提取指定位段 |
| `IS_BIT_SET(value, bitOffset)` | 检查指定位是否置位 |
| `PACK_BITS(bits, offset, flags, value)` | 将值打包到指定位段 |
| `PACK_BITS_UNSAFE(...)` | 不做范围检查的位打包 |
| `FALCOR_GPU_CACHE_SIZE` | GPU 缓存行大小（128 字节） |
| `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` | 命名空间包装宏 |
| `SETTER_DECL` | CPU 端为空，GPU 端为 `[mutating]` |
| `CONST_FUNCTION` | CPU 端为 `const`，GPU 端为空 |

## 依赖关系

### import

CPU 端引用：
- `Core/Enum.h`
- `Utils/Math/ScalarMath.h`
- `Utils/Math/Vector.h`
- `Utils/Math/Matrix.h`

## 实现细节

- 通过 `__STDC_HOSTED__` 或 `__cplusplus` 检测是否处于主机编译环境
- GPU 端将 `inline` 和 `constexpr` 重定义为空或 `const`，以兼容 Slang 语法
- `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 在 GPU 端定义为空操作
