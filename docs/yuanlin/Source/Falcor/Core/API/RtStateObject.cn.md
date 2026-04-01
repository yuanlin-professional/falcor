# RtStateObject 源码文档

> 路径: `Source/Falcor/Core/API/RtStateObject.h` / `RtStateObject.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

RtStateObject 封装了光线追踪管线状态对象（RTPSO），包含着色器程序内核、最大追踪递归深度和管线标志。

## 类与接口

### `RtStateObjectDesc`（结构体）
| 成员 | 类型 | 说明 |
|------|------|------|
| `pProgramKernels` | `ref<const ProgramKernels>` | 着色器程序内核 |
| `maxTraceRecursionDepth` | `uint32_t` | 最大光线追踪递归深度 |
| `pipelineFlags` | `RtPipelineFlags` | 光线追踪管线标志 |

### `RtStateObject`
- **继承**: `Object`
- **职责**: 管理光线追踪管线状态

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::IPipelineState* getGfxPipelineState() const` | 获取 GFX 管线状态 |
| `const ref<const ProgramKernels>& getKernels() const` | 获取着色器内核 |
| `uint32_t getMaxTraceRecursionDepth() const` | 获取最大递归深度 |
| `void const* getShaderIdentifier(uint32_t index) const` | 获取着色器标识符 |

## 依赖关系
### 本文件引用
- `Handles.h`, `Raytracing.h`, `Core/Program/ProgramVersion.h`

### 被以下文件引用
- `Device.h`, `RenderContext.cpp`
