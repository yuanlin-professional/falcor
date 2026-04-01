# Texture 源码文档

> 路径: `Source/Falcor/Core/API/Texture.h` / `Texture.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Texture 类是 GPU 纹理资源的抽象，支持 1D、2D、3D、Cube 和多重采样纹理。提供纹理属性查询、子资源管理、Mip 链生成、文件加载/保存、资源视图获取以及 Python（NumPy/PyTorch）互操作。

## 类与接口

### `Texture`
- **继承**: `Resource`
- **职责**: GPU 纹理资源管理

#### 内部结构体 `SubresourceLayout`
| 成员 | 说明 |
|------|------|
| `rowSize` | 单行字节数（未对齐） |
| `rowSizeAligned` | 单行字节数（已对齐） |
| `rowCount` | 行数 |
| `depth` | 深度切片数 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `uint32_t getWidth(uint32_t mipLevel = 0) const` | 获取指定 mip 级别的宽度 |
| `uint32_t getHeight(uint32_t mipLevel = 0) const` | 获取指定 mip 级别的高度 |
| `uint32_t getDepth(uint32_t mipLevel = 0) const` | 获取指定 mip 级别的深度 |
| `uint32_t getMipCount() const` | 获取 mip 级别数量 |
| `uint32_t getArraySize() const` | 获取数组大小 |
| `uint32_t getSampleCount() const` | 获取采样数量 |
| `ResourceFormat getFormat() const` | 获取资源格式 |
| `ref<ShaderResourceView> getSRV(...)` | 获取着色器资源视图 |
| `ref<UnorderedAccessView> getUAV(...)` | 获取无序访问视图 |
| `ref<RenderTargetView> getRTV(...)` | 获取渲染目标视图 |
| `ref<DepthStencilView> getDSV(...)` | 获取深度-模板视图 |
| `void generateMips(RenderContext*)` | 生成 mip 链 |
| `void captureToFile(...)` | 保存到文件 |
| `SubresourceLayout getSubresourceLayout(uint32_t) const` | 获取子资源布局 |

## 依赖关系
### 本文件引用
- `Resource.h`, `ResourceViews.h`, `Formats.h`, `Handles.h`
- `Utils/Image/Bitmap.h`

### 被以下文件引用
- `FBO.h`, `RenderContext.h`, `CopyContext.h`, `Swapchain.h`, `Device.h`
