# Resource 源码文档

> 路径: `Source/Falcor/Core/API/Resource.h` / `Resource.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Resource 是 Buffer 和 Texture 的抽象基类，定义了 GPU 资源的通用接口：资源类型、资源状态管理（全局状态和子资源级别状态）、绑定标志、资源视图缓存和原生句柄获取。

## 类与接口

### `Resource`
- **继承**: `Object`（纯虚类）
- **职责**: GPU 资源的抽象基类

#### 内部枚举
| 枚举 | 说明 |
|------|------|
| `Type` | 资源类型：Buffer、Texture1D、Texture2D、Texture3D、TextureCube、Texture2DMultisample |
| `State` | 资源状态：Undefined、Common、VertexBuffer、ConstantBuffer、RenderTarget、UnorderedAccess、DepthStencil、ShaderResource、CopyDest、CopySource、AccelerationStructure 等 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `Type getType() const` | 获取资源类型 |
| `ResourceBindFlags getBindFlags() const` | 获取绑定标志 |
| `State getGlobalState() const` | 获取全局资源状态 |
| `void setGlobalState(State) const` | 设置全局资源状态 |
| `virtual ref<ShaderResourceView> getSRV()` | 获取默认 SRV |
| `virtual ref<UnorderedAccessView> getUAV()` | 获取默认 UAV |
| `virtual gfx::IResource* getGfxResource() const` | 获取 GFX 资源接口 |
| `NativeHandle getNativeHandle() const` | 获取原生 API 句柄 |
| `void invalidateViews()` | 使所有缓存的视图失效 |

## 依赖关系
### 本文件引用
- `Handles.h`, `NativeHandle.h`, `Formats.h`, `ResourceViews.h`

### 被以下文件引用
- `Buffer.h`（继承）, `Texture.h`（继承）, `CopyContext.h`
