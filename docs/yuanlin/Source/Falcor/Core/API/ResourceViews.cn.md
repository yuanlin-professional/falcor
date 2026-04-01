# ResourceViews 源码文档

> 路径: `Source/Falcor/Core/API/ResourceViews.h` / `ResourceViews.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

ResourceViews 定义了 GPU 资源视图的抽象层，包括着色器资源视图（SRV）、无序访问视图（UAV）、渲染目标视图（RTV）和深度-模板视图（DSV）。资源视图控制着色器如何访问底层资源的特定子区域。

## 类与接口

### `ResourceViewInfo`（结构体）
- **职责**: 描述资源视图的范围（纹理的 mip 级别和数组切片，或缓冲区的偏移和大小）

### `ResourceView`（基类）
- **继承**: `Object`
- **职责**: 所有资源视图的抽象基类

### `ShaderResourceView`
- **继承**: `ResourceView`
- **职责**: 着色器资源视图（只读访问）

### `UnorderedAccessView`
- **继承**: `ResourceView`
- **职责**: 无序访问视图（读写访问）

### `RenderTargetView`
- **继承**: `ResourceView`
- **职责**: 渲染目标视图

### `DepthStencilView`
- **继承**: `ResourceView`
- **职责**: 深度-模板视图

#### 通用关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<XXXView> create(Device*, Resource*, ...)` | 创建视图 |
| `gfx::IResourceView* getGfxResourceView() const` | 获取 GFX 资源视图 |
| `NativeHandle getNativeHandle() const` | 获取原生句柄 |
| `const Resource* getResource() const` | 获取关联的资源 |
| `const ResourceViewInfo& getViewInfo() const` | 获取视图信息 |

## 依赖关系
### 本文件引用
- `Handles.h`, `NativeHandle.h`, `Core/Object.h`, `Core/Program/ProgramReflection.h`

### 被以下文件引用
- `Resource.h`, `Buffer.h`, `Texture.h`, `FBO.h`, `CopyContext.h`, `RenderContext.h`
