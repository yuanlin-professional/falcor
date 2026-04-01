# ComputeContext 源码文档

> 路径: `Source/Falcor/Core/API/ComputeContext.h` / `ComputeContext.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

ComputeContext 继承自 CopyContext，在复制操作基础上增加了计算着色器调度和 UAV 清除功能。它是渲染上下文层级结构中的中间层，为计算管线操作提供接口。

## 类与接口

### `ComputeContext`
- **继承**: `CopyContext`
- **职责**: 提供计算着色器调度、间接调度和 UAV 清除操作

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `void dispatch(ComputeState*, ProgramVars*, const uint3& dispatchSize)` | 调度计算任务 |
| `void dispatchIndirect(ComputeState*, ProgramVars*, const Buffer*, uint64_t)` | 间接调度计算任务（参数从缓冲区读取） |
| `void clearUAV(const UnorderedAccessView*, const float4& value)` | 以浮点值清除 UAV |
| `void clearUAV(const UnorderedAccessView*, const uint4& value)` | 以无符号整数清除 UAV |
| `void clearUAVCounter(const ref<Buffer>&, uint32_t value)` | 清除结构化缓冲区的 UAV 计数器 |
| `void submit(bool wait = false) override` | 提交命令列表 |

## 实现细节

- `dispatch` 先准备描述符集合，然后通过 GFX 计算编码器绑定管线并调度
- `dispatchIndirect` 额外需要对参数缓冲区设置 `IndirectArg` 状态屏障
- `submit` 调用基类提交后重置 `mpLastBoundComputeVars` 缓存

## 依赖关系
### 本文件引用
- `CopyContext.h`, `Buffer.h`, `LowLevelContextData.h`
- `Core/State/ComputeState.h`, `Core/Program/ProgramVars.h`

### 被以下文件引用
- `RenderContext.h`（RenderContext 继承自 ComputeContext）
