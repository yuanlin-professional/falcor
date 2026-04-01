# SharedTypes.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/SharedTypes.slang`
> 类型: Slang/C++ 共享头文件
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件定义了可微渲染系统中 CPU 和 GPU 共享的基础类型，包括微分模式、梯度类型、聚合模式、调试变量类型等枚举和结构体。这些类型被整个可微渲染模块广泛使用，是连接 C++ 主机端和 Slang 着色器端的类型桥梁。使用 `HostDeviceShared.slangh` 实现跨平台兼容。

## 结构体与接口

### `DiffMode` (枚举类)

| 值 | 说明 |
|-----|------|
| `Primal` | 原始模式，输出普通路径追踪图像 |
| `BackwardDiff` | 反向微分模式，输出梯度向量 |
| `ForwardDiffDebug` | 前向调试模式，输出单变量梯度图像用于调试 |
| `BackwardDiffDebug` | 反向调试模式，输出单变量梯度图像用于调试 |

### `GradientType` (枚举类)

| 值 | 说明 |
|-----|------|
| `Material` | 材质参数梯度 |
| `MeshPosition` | 网格顶点位置梯度 |
| `MeshNormal` | 网格顶点法线梯度 |
| `MeshTangent` | 网格顶点切线梯度 |
| `Count` | 梯度类型总数 |

### `GradientAggregateMode` (枚举类)

| 值 | 说明 |
|-----|------|
| `Direct` | 直接聚合模式 |
| `HashGrid` | 哈希网格聚合模式（避免原子操作冲突） |

### `DiffVariableType` (枚举类)

| 值 | 说明 |
|-----|------|
| `None` | 无 |
| `Material` | 材质变量 |
| `GeometryTranslation` | 几何平移变量 |

### `DiffDebugParams` (结构体)

- **职责**: 可微渲染调试参数

| 成员 | 类型 | 说明 |
|------|------|------|
| `varType` | `DiffVariableType` | 调试的可微变量类型 |
| `id` | `uint2` | 目标标识 |
| `offset` | `uint32_t` | 参数偏移量 |
| `grad` | `float4` | 调试梯度值 |

### `InverseOptimizationParams` (结构体)

- **职责**: 逆渲染优化参数

| 成员 | 类型 | 说明 |
|------|------|------|
| `meshID` | `uint32_t` | 目标网格 ID |
| `_pad0` | `uint3` | 对齐填充 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Utils/HostDeviceShared.slangh` | CPU/GPU 共享宏定义 |

### 被以下文件引用

- `DiffRendering/DiffDebugParams.slang`
- `DiffRendering/InverseOptimizationParams.slang`
- `DiffRendering/SceneGradients.slang`
- `DiffRendering/SceneGradients.h`
- `DiffRendering/AggregateGradients.cs.slang`
- 以及所有通过上述文件间接引用的可微渲染着色器
