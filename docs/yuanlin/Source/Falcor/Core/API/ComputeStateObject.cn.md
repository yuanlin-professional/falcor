# ComputeStateObject 源码文档

> 路径: `Source/Falcor/Core/API/ComputeStateObject.h` / `ComputeStateObject.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

ComputeStateObject 封装了计算管线状态对象（PSO），将着色器程序内核与底层 GFX 管线状态关联。支持可选的 D3D12 根签名覆盖。

## 类与接口

### `ComputeStateObjectDesc`（结构体）
| 成员 | 类型 | 说明 |
|------|------|------|
| `pProgramKernels` | `ref<const ProgramKernels>` | 着色器程序内核 |
| `pD3D12RootSignatureOverride` | `ref<const D3D12RootSignature>` | （D3D12）可选的根签名覆盖 |

### `ComputeStateObject`
- **继承**: `Object`
- **职责**: 管理计算管线状态对象

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::IPipelineState* getGfxPipelineState() const` | 获取 GFX 管线状态 |
| `NativeHandle getNativeHandle() const` | 获取原生 API 句柄（D3D12: `ID3D12PipelineState*`, Vulkan: `VkPipeline`） |
| `const ComputeStateObjectDesc& getDesc() const` | 获取描述符 |

## 依赖关系
### 本文件引用
- `Device.h`, `GFXAPI.h`, `NativeHandleTraits.h`, `Core/Program/ProgramVersion.h`

### 被以下文件引用
- `ComputeContext.cpp`（绑定计算管线状态）
