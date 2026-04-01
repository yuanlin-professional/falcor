# SampleGeneratorInterface.slang 源码文档

> 路径: `Source/Falcor/Utils/Sampling/SampleGeneratorInterface.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Sampling

## 功能概述

定义了 GPU 端采样生成器的 **Slang 接口** (`ISampleGenerator`) 以及一组便捷的泛型全局函数，用于从任何实现了该接口的采样器中生成 1D/2D/3D/4D 的 [0, 1) 范围浮点采样值。

## 结构体与接口

### `ISampleGenerator`（接口）

所有 GPU 采样生成器必须实现的接口。

| 方法签名 | 说明 |
|----------|------|
| `[mutating] uint next()` | 返回下一个采样值（32 位无符号整数），同时更新内部状态 |

## 函数

### 泛型便捷函数

这些函数是全局泛型函数（而非接口的成员函数），因为 Slang 目前不支持在接口中定义带有默认实现的成员函数。

| 函数签名 | 说明 |
|----------|------|
| `float sampleNext1D<S : ISampleGenerator>(inout S sg)` | 从采样器获取一个 [0, 1) 范围的 1D 浮点值。使用高 24 位除以 2^24，确保 `1.0 - u != 0.0` |
| `float2 sampleNext2D<S : ISampleGenerator>(inout S sg)` | 获取 2D 采样值。按固定顺序（x, y）调用 `sampleNext1D` 以确保求值顺序一致 |
| `float3 sampleNext3D<S : ISampleGenerator>(inout S sg)` | 获取 3D 采样值。按固定顺序（x, y, z）调用 |
| `float4 sampleNext4D<S : ISampleGenerator>(inout S sg)` | 获取 4D 采样值。按固定顺序（x, y, z, w）调用 |

## 依赖关系 / import

- 无外部依赖（基础接口定义文件）

## 实现细节

- `sampleNext1D` 的实现 `(bits >> 8) * 0x1p-24`：
  - 右移 8 位取高 24 位，乘以 2^-24 映射到 [0, 1)
  - 使用高 24 位而非低位是因为许多伪随机数生成器的低位统计特性较差
  - 24 位精度与 IEEE 754 float 的尾数精度（23 位 + 隐含 1 位）匹配，确保不会产生 1.0
- 2D/3D/4D 函数显式按分量赋值（而非使用向量初始化器），以避免编译器对求值顺序的不确定性
- 所有函数使用 `inout` 参数修饰符，因为采样操作会修改生成器的内部状态
