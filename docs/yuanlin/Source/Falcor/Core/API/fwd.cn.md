# fwd 源码文档

> 路径: `Source/Falcor/Core/API/fwd.h`
> 类型: C++ 头文件（前向声明）
> 模块: Core/API

## 功能概述

fwd.h 提供了 Core/API 模块中核心类型的前向声明，避免不必要的头文件循环依赖。包含的前向声明涵盖了设备、上下文、资源、视图、同步和管线状态对象等类型。

## 前向声明列表

| 类/结构体 | 说明 |
|-----------|------|
| `Device` | GPU 设备 |
| `CopyContext` | 复制上下文 |
| `ComputeContext` | 计算上下文 |
| `RenderContext` | 渲染上下文 |
| `Resource` | 资源基类 |
| `Buffer` | 缓冲区 |
| `Texture` | 纹理 |
| `Sampler` | 采样器 |
| `FenceDesc` | Fence 描述符 |
| `Fence` | 同步 Fence |
| `ComputeStateObjectDesc` | 计算管线状态描述 |
| `ComputeStateObject` | 计算管线状态对象 |
| `GraphicsStateObjectDesc` | 图形管线状态描述 |
| `GraphicsStateObject` | 图形管线状态对象 |
| `RtStateObjectDesc` | 光线追踪管线状态描述 |
| `RtStateObject` | 光线追踪管线状态对象 |
| `ShaderResourceView` | 着色器资源视图 |
| `UnorderedAccessView` | 无序访问视图 |

## 依赖关系
### 本文件引用
- `Core/Object.h`

### 被以下文件引用
- 大部分 Core/API 头文件使用 fwd.h 代替完整头文件包含
