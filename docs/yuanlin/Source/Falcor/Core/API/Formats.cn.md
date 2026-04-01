# Formats 源码文档

> 路径: `Source/Falcor/Core/API/Formats.h` / `Formats.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Formats 模块定义了 Falcor 的资源格式系统，包括纹理通道标志、资源绑定标志和所有支持的资源格式枚举。还提供了格式查询工具函数（字节大小、通道数量、类型判断等）。

## 类与接口

### `TextureChannelFlags`（枚举）
纹理颜色通道标志：None、Red、Green、Blue、Alpha、RGB、RGBA。

### `ResourceBindFlags`（枚举）
资源绑定标志，指示资源将被绑定到管线的哪些阶段：

| 标志 | 说明 |
|------|------|
| `None` | 暂存资源 |
| `Vertex` | 顶点缓冲区 |
| `Index` | 索引缓冲区 |
| `Constant` | 常量缓冲区 |
| `ShaderResource` | 着色器资源 |
| `UnorderedAccess` | 无序访问（UAV） |
| `RenderTarget` | 渲染目标 |
| `DepthStencil` | 深度-模板缓冲区 |
| `IndirectArg` | 间接参数缓冲区 |
| `Shared` | 跨适配器共享（主要用于 CUDA 共享） |
| `AccelerationStructure` | 加速结构 |

### `ResourceFormat`（枚举）
完整的资源格式列表，包括 Unorm、Snorm、Float、Int、Uint 各种位深和通道组合，以及压缩格式（BC1-BC7）和深度格式。

### 工具函数
| 函数 | 说明 |
|------|------|
| `getFormatBytesPerBlock()` | 获取每块字节数 |
| `getFormatChannelCount()` | 获取通道数量 |
| `isDepthFormat()` / `isStencilFormat()` / `isCompressedFormat()` | 格式类型判断 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`, `Core/Error.h`, `Core/Enum.h`

### 被以下文件引用
- 几乎所有涉及资源创建的文件（`Buffer.h`, `Texture.h`, `Device.h`, `FBO.h` 等）
