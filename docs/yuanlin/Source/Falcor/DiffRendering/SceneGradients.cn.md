# SceneGradients 源码文档

> 路径: `Source/Falcor/DiffRendering/SceneGradients.h` + `Source/Falcor/DiffRendering/SceneGradients.cpp`
> 类型: C++ 头文件 + 实现
> 模块: 可微渲染 (DiffRendering)

## 功能概述

`SceneGradients` 是可微渲染系统中管理场景参数梯度缓冲区的核心 C++ 类。它负责创建和管理多种梯度类型（材质、网格位置、网格法线、网格切线）对应的 GPU 缓冲区，支持通过哈希网格避免原子操作冲突，并提供梯度清零和聚合功能。该类还暴露了完整的 Python 绑定接口，供脚本化逆渲染流程使用。

## 类与接口

### `SceneGradients`

- **继承**: `Object`（Falcor 引用计数基类）
- **职责**: 管理可微渲染中各类场景参数的梯度 GPU 缓冲区，提供梯度清零、聚合和数据访问接口

#### 内嵌类型

##### `GradConfig`

| 成员 | 类型 | 说明 |
|------|------|------|
| `type` | `GradientType` | 梯度类型 |
| `dim` | `uint32_t` | 梯度维度（参数总数） |
| `hashSize` | `uint32_t` | 哈希表大小 |

##### `GradInfo` (私有)

| 成员 | 类型 | 说明 |
|------|------|------|
| `active` | `bool` | 是否激活 |
| `dim` | `uint32_t` | 梯度维度 |
| `hashSize` | `uint32_t` | 哈希表大小 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SceneGradients(ref<Device>, vector<GradConfig>&, GradientAggregateMode)` | 构造函数，初始化梯度缓冲区和聚合计算通道 |
| `static ref<SceneGradients> create(ref<Device>, vector<GradConfig>&)` | 工厂方法，默认使用 HashGrid 聚合模式 |
| `void bindShaderData(const ShaderVar& var)` | 绑定参数块到着色器变量 |
| `void clearGrads(RenderContext*, GradientType)` | 清零指定类型的梯度缓冲区 |
| `void aggregateGrads(RenderContext*, GradientType)` | 聚合指定类型的临时梯度到最终缓冲区 |
| `void clearAllGrads(RenderContext*)` | 清零所有激活的梯度缓冲区 |
| `void aggregateAllGrads(RenderContext*)` | 聚合所有激活的梯度 |
| `uint32_t getGradDim(GradientType)` | 获取梯度维度 |
| `uint32_t getHashSize(GradientType)` | 获取哈希表大小 |
| `const ref<Buffer>& getTmpGradsBuffer(GradientType)` | 获取临时梯度缓冲区 |
| `const ref<Buffer>& getGradsBuffer(GradientType)` | 获取最终梯度缓冲区 |
| `vector<GradientType> getActiveGradTypes()` | 获取所有激活的梯度类型列表 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mGradInfos` | `array<GradInfo, Count>` | 各梯度类型的配置信息 |
| `mAggregateMode` | `GradientAggregateMode` | 聚合模式（Direct/HashGrid） |
| `mpSceneGradientsBlock` | `ref<ParameterBlock>` | 着色器参数块 |
| `mpGrads[]` | `ref<Buffer>[]` | 最终梯度缓冲区数组 |
| `mpTmpGrads[]` | `ref<Buffer>[]` | 临时梯度缓冲区数组 |
| `mpAggregatePass` | `ref<ComputePass>` | 梯度聚合计算通道 |

#### Python 绑定

| Python 方法 | 说明 |
|-------------|------|
| `SceneGradients.create(device, grad_config_list)` | 静态创建方法 |
| `clear_grads(render_context, grad_type)` | 清零梯度 |
| `aggregate_grads(render_context, grad_type)` | 聚合梯度（含 CUDA 同步） |
| `clear_all_grads(render_context)` | 清零全部梯度 |
| `aggregate_all_grads(render_context)` | 聚合全部梯度（含 CUDA 同步） |
| `get_grad_types()` | 获取激活的梯度类型列表 |
| `get_grads_buffer(grad_type)` | 获取梯度缓冲区 |

## 依赖关系

### 本文件引用

- `Core/API/ParameterBlock.h` — 参数块
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `SharedTypes.slang` — 共享类型（`GradientType`, `GradientAggregateMode` 等）
- `Core/Error.h` — 错误处理
- `DiffRendering/SceneGradients.slang` — 着色器端场景梯度定义
- `DiffRendering/AggregateGradients.cs.slang` — 聚合计算着色器

### 被以下文件引用

- 可微路径追踪渲染通道等使用可微渲染的模块

## 实现细节

- 构造时根据 `GradConfig` 列表初始化各类型的梯度信息，为每种激活的梯度类型创建两个 GPU 缓冲区：`mpGrads`（最终梯度，大小 = dim * sizeof(float)）和 `mpTmpGrads`（临时梯度，大小 = dim * hashSize * sizeof(float)）
- 通过 `createParameterBlock()` 创建着色器参数块，加载 `SceneGradients.slang` 获取反射信息并绑定缓冲区
- 聚合操作通过调度 `AggregateGradients.cs.slang` 计算着色器完成，Direct 模式直接复制，HashGrid 模式使用原子加操作
- Python 绑定中的 `aggregate` 和 `aggregateAll` 函数在 CUDA 可用时会额外调用 `waitForFalcor()` 确保同步
