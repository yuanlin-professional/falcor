# LowLevelContextData 源码文档

> 路径: `Source/Falcor/Core/API/LowLevelContextData.h` / `LowLevelContextData.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

LowLevelContextData 管理底层命令缓冲区和各类命令编码器。它是命令录制的核心基础设施，被 CopyContext 及其子类内部持有，负责命令缓冲区的打开、关闭和提交，以及获取不同类型的命令编码器。

## 类与接口

### `LowLevelContextData`
- **职责**: 管理命令队列、命令缓冲区和命令编码器

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::ICommandQueue* getGfxCommandQueue() const` | 获取 GFX 命令队列 |
| `gfx::ICommandBuffer* getGfxCommandBuffer() const` | 获取 GFX 命令缓冲区 |
| `NativeHandle getCommandQueueNativeHandle() const` | 获取原生命令队列句柄 |
| `NativeHandle getCommandBufferNativeHandle() const` | 获取原生命令缓冲区句柄 |
| `void openCommandBuffer()` | 打开命令缓冲区 |
| `void closeCommandBuffer()` | 关闭命令缓冲区 |
| `void submitCommandBuffer()` | 提交命令缓冲区 |
| `gfx::IResourceCommandEncoder* getResourceCommandEncoder()` | 获取资源命令编码器 |
| `gfx::IComputeCommandEncoder* getComputeCommandEncoder()` | 获取计算命令编码器 |
| `gfx::IRenderCommandEncoder* getRenderCommandEncoder(...)` | 获取渲染命令编码器 |
| `gfx::IRayTracingCommandEncoder* getRayTracingCommandEncoder()` | 获取光线追踪命令编码器 |
| `void beginDebugEvent(const char* name)` | 开始调试事件 |
| `void endDebugEvent()` | 结束调试事件 |

## 实现细节

- 编码器之间互斥：获取新编码器时自动关闭上一个
- 命令缓冲区提交后自动重新打开新的命令缓冲区
- CUDA 互操作支持共享 Fence 和外部信号量

## 依赖关系
### 本文件引用
- `Fence.h`, `Handles.h`, `NativeHandle.h`

### 被以下文件引用
- `CopyContext.h`, `ComputeContext.h`, `RenderContext.h`
