# splat2d.cs.slang 着色器文档

> 路径: `scripts/python/gaussian2d/splat2d.cs.slang`
> 类型: Slang 计算着色器
> 模块: scripts/python/gaussian2d

## 功能概述

该计算着色器实现了二维高斯 Splatting 的前向渲染和反向传播（梯度计算）。每个高斯斑点（Blob）具有位置、缩放、颜色和旋转属性，着色器通过高斯函数计算每个像素受到的颜色贡献。前向 pass 累加所有斑点对图像的颜色贡献；反向 pass 利用 Slang 的自动微分功能（`bwd_diff`）计算每个斑点参数的梯度，并通过 Wave 内归约和原子操作高效写回梯度缓冲区。着色器提供两个入口点，线程组大小均为 16x16x1。

## 常量

| 名称 | 类型 | 说明 |
|------|------|------|
| `kSkipDistance` | `float`（static const） | 距离跳过阈值（0.1），超过此距离的斑点在反向 pass 中被跳过以提升性能 |

## 结构体与接口

### `Blob` (实现 `IDifferentiable`)

| 字段 | 类型 | 说明 |
|------|------|------|
| `pos` | `float2` | 高斯斑点的二维位置 |
| `scale` | `float2` | 高斯斑点在 X/Y 方向的缩放 |
| `color` | `float3` | 高斯斑点的 RGB 颜色 |
| `rotation` | `float` | 高斯斑点的旋转角度（弧度） |

### `Forward`

| 字段 | 类型 | 说明 |
|------|------|------|
| `blobs` | `StructuredBuffer<Blob>` | 只读结构化缓冲区，存储所有斑点数据 |
| `blob_count` | `uint` | 斑点总数 |
| `image` | `RWStructuredBuffer<float3>` | 可读写结构化缓冲区，存储输出图像（按行优先线性排列） |
| `resolution` | `uint2` | 输出图像分辨率 |

### `Backward`

| 字段 | 类型 | 说明 |
|------|------|------|
| `blobs` | `StructuredBuffer<Blob>` | 只读结构化缓冲区，存储所有斑点数据 |
| `grad_blobs` | `RWByteAddressBuffer` | 可读写字节地址缓冲区，用于存储斑点参数的梯度（通过原子加法累加） |
| `blob_count` | `uint` | 斑点总数 |
| `grad_image` | `StructuredBuffer<float3>` | 只读结构化缓冲区，存储图像的梯度 |
| `resolution` | `uint2` | 图像分辨率 |

## 全局资源

| 名称 | 类型 | 说明 |
|------|------|------|
| `forward` | `ParameterBlock<Forward>` | 前向 pass 的参数块 |
| `backward` | `ParameterBlock<Backward>` | 反向 pass 的参数块 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float2x2 inverse(float2x2 M)` | 计算 2x2 矩阵的逆矩阵（支持反向微分） |
| `float2x2 rotation_matrix(float angle)` | 根据角度构建 2x2 旋转矩阵（支持反向微分） |
| `float2x2 scale_matrix(float2 scale)` | 根据缩放值构建 2x2 缩放矩阵（支持反向微分） |
| `float gaussian(float2 x, float2x2 sigma)` | 计算二维高斯函数值，sigma 为协方差矩阵（支持反向微分） |
| `float3 eval(no_diff float2 pos, Blob blob)` | 评估单个斑点对给定位置的颜色贡献，内部构建旋转-缩放协方差矩阵并调用高斯函数（支持反向微分） |
| `void Forward.forward(uint2 idx)` | 前向渲染：累加所有斑点对当前像素的颜色贡献 |
| `void Backward.write_grad(uint offset, float value)` | 使用 Wave 内归约求和后通过 `InterlockedAddF32` 原子写入梯度 |
| `void Backward.backward(uint2 idx)` | 反向传播：对每个斑点调用 `bwd_diff(eval)` 计算梯度并写入梯度缓冲区 |
| `void forward_main(uint3 tid: SV_DispatchThreadID)` | 前向计算着色器入口点 |
| `void backward_main(uint3 tid: SV_DispatchThreadID)` | 反向计算着色器入口点 |

## 依赖关系

### import

- `Utils/NVAPI.slangh`（通过 `#include`） -- 提供 NVAPI 扩展支持，用于 `InterlockedAddF32` 等原子浮点操作
