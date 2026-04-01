# EnvMapSampler 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EnvMapSampler.h` + `EnvMapSampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件实现了环境贴图采样器的 CPU 端管理类。该类负责加载和预处理环境贴图，构建分层重要性贴图（Hierarchical Importance Map）用于 GPU 端的重要性采样。重要性贴图基于环境贴图的亮度（luminance）构建，采用 mipmap 金字塔结构，从 NxN 到 1x1 分辨率，使得 GPU 着色器可以高效地进行自顶向下的层次采样。

## 类与接口

### `EnvMapSampler`

- **继承**: 无
- **职责**: 管理环境贴图采样所需的 GPU 资源，构建重要性贴图

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `EnvMapSampler(ref<Device> pDevice, ref<EnvMap> pEnvMap)` | 构造函数，创建计算通道、采样器并生成重要性贴图 |
| `void bindShaderData(const ShaderVar& var) const` | 绑定 `importanceBaseMip`、`importanceInvDim`、`importanceMap`、`importanceSampler` 到着色器 |
| `const ref<EnvMap>& getEnvMap() const` | 获取环境贴图引用 |
| `const ref<Texture>& getImportanceMap() const` | 获取重要性贴图纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mpEnvMap` | `ref<EnvMap>` | 环境贴图 |
| `mpSetupPass` | `ref<ComputePass>` | 构建重要性贴图的计算通道 |
| `mpImportanceMap` | `ref<Texture>` | 分层重要性贴图（亮度），R32Float 格式 |
| `mpImportanceSampler` | `ref<Sampler>` | 点采样+边缘夹取的采样器 |

## 实现细节

- **构造流程**: 创建计算着色器通道 -> 创建点采样器 -> 调用 `createImportanceMap` 生成重要性贴图
- **createImportanceMap()**:
  - 默认分辨率 512x512，每纹素 64 个子采样（8x8）
  - 创建带有完整 mipmap 链的 R32Float 纹理
  - 通过 `EnvMapSamplerSetup.cs.slang` 计算着色器计算基层 mip
  - 使用 `generateMips()` 自动生成 mipmap 层次
- **bindShaderData()**: 设置 `importanceBaseMip` 为最高 mip 级别（1x1 分辨率），`importanceInvDim` 为维度倒数

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义
- `Core/API/Texture.h` — 纹理 API
- `Core/API/Sampler.h` — 采样器 API
- `Core/API/RenderContext.h` — 渲染上下文（仅 .cpp）
- `Core/Pass/ComputePass.h` — 计算通道
- `Core/Error.h` — 错误处理（仅 .cpp）
- `Scene/Lights/EnvMap.h` — 环境贴图

### 被以下文件引用

- GPU 端的 `EnvMapSampler.slang` 使用本类绑定的数据进行采样
