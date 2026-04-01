# BSDFIntegrator 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFIntegrator.h`, `BSDFIntegrator.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Materials

## 功能概述

提供双向散射分布函数（BSDF）在半球上的数值积分工具。通过 GPU 计算着色器对任意材质的各向同性 BSDF 进行蒙特卡洛积分，支持批量入射方向处理。

## 类与接口

### `BSDFIntegrator`

- **继承**: 无
- **职责**: 管理 GPU 资源，执行 BSDF 半球积分的计算流水线

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BSDFIntegrator(ref<Device>, const ref<Scene>&)` | 构造函数，要求 Shader Model 6.6 支持 |
| `float3 integrateIsotropic(RenderContext*, MaterialID, float)` | 对单个入射方向积分 BSDF |
| `vector<float3> integrateIsotropic(RenderContext*, MaterialID, const vector<float>&)` | 对多个入射方向批量积分 BSDF |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpIntegrationPass` | `ref<ComputePass>` | 积分计算通道 |
| `mpFinalPass` | `ref<ComputePass>` | 最终归约通道 |
| `mpCosThetaBuffer` | `ref<Buffer>` | 入射角 cos(theta) 缓冲区 |
| `mpResultBuffer` | `ref<Buffer>` | 中间结果缓冲区 |
| `mpFinalResultBuffer` | `ref<Buffer>` | 最终结果缓冲区 |
| `mpStagingBuffer` | `ref<Buffer>` | 回读暂存缓冲区 |
| `mResultCount` | `uint32_t` | 每个积分网格的中间结果数量 |

## 依赖关系

### 本文件引用
- `Core/Macros.h`, `Core/API/Buffer.h`, `Core/API/Fence.h`
- `Core/Pass/ComputePass.h`
- `Scene/Scene.h`, `Scene/SceneIDs.h`
- `Core/API/RenderContext.h`
- `Utils/Logger.h`

### 被以下文件引用
- GPU 着色器 `BSDFIntegrator.cs.slang`

## 实现细节

- 积分网格大小固定为 512x512，每个网格单元取 8x8 分层采样，总计 4096x4096 个半球采样
- 使用两遍 GPU 计算：第一遍并行积分，第二遍线程组内归约
- 线程组大小为 32x32（第一遍）和 256（第二遍）
- 结果通过暂存缓冲区回读到 CPU
