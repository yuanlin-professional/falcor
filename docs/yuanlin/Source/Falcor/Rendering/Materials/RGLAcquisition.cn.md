# RGLAcquisition 源码文档

> 路径: `Source/Falcor/Rendering/Materials/RGLAcquisition.h`, `RGLAcquisition.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Materials

## 功能概述

提供 BRDF 虚拟测量工具，将任意材质的 BRDF 转换为 Dupuy & Jakob 提出的自适应参数化格式（RGL 格式）。整个流程通过多遍 GPU 计算着色器实现。

## 类与接口

### `RGLAcquisition`

- **继承**: 无
- **职责**: 管理虚拟 BRDF 测量的完整流水线

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `RGLAcquisition(ref<Device>, const ref<Scene>&)` | 构造函数，创建所有计算通道和缓冲区 |
| `acquireIsotropic(RenderContext*, MaterialID)` | 执行各向同性 BRDF 虚拟测量 |
| `toRGLFile()` | 将测量结果导出为 RGLFile 格式 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpRetroReflectionPass` | `ref<ComputePass>` | 回溯反射测量通道 |
| `mpBuildKernelPass` | `ref<ComputePass>` | Fredholm 核矩阵构建通道 |
| `mpPowerIterationPass` | `ref<ComputePass>` | 幂迭代提取 NDF 通道 |
| `mpIntegrateSigmaPass` | `ref<ComputePass>` | 微表面投影面积积分通道 |
| `mpComputeVNDFPass` | `ref<ComputePass>` | VNDF 计算通道 |
| `mpAcquireBRDFPass` | `ref<ComputePass>` | BRDF 测量通道 |

## 实现细节

测量流程分为 5 个阶段：
1. 测量回溯反射并构建 Fredholm 问题的核矩阵
2. 幂迭代提取最大特征向量（即 NDF）
3. 数值积分 NDF 对应的微表面投影面积
4. 计算 VNDF 并使其可采样
5. 执行实际 BRDF 测量

## 依赖关系

### 本文件引用
- `Core/Macros.h`, `Core/API/Buffer.h`, `Core/Pass/ComputePass.h`
- `Scene/Scene.h`, `Scene/Material/RGLFile.h`
- GPU 着色器 `RGLAcquisition.cs.slang`
