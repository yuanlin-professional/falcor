# FBO 源码文档

> 路径: `Source/Falcor/Core/API/FBO.h` / `FBO.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

FBO（Fbo）类是帧缓冲对象的底层抽象，管理颜色附件和深度-模板附件的绑定。支持多种创建方式：空帧缓冲、从纹理列表创建、2D/Cubemap 帧缓冲工厂方法。支持多重采样和可编程采样位置。

## 类与接口

### `Fbo`
- **继承**: `Object`
- **职责**: 管理帧缓冲的创建、附件绑定和资源视图

#### 内部类 `Desc`
- **职责**: 描述帧缓冲的格式、采样数和 UAV 兼容性

| 方法 | 说明 |
|------|------|
| `Desc& setColorTarget(uint32_t, ResourceFormat, bool allowUav)` | 设置颜色目标格式 |
| `Desc& setDepthStencilTarget(ResourceFormat, bool allowUav)` | 设置深度-模板目标格式 |
| `Desc& setSampleCount(uint32_t)` | 设置采样数 |

#### 关键静态工厂方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<Fbo> create(ref<Device>)` | 创建空帧缓冲 |
| `static ref<Fbo> create(ref<Device>, const vector<ref<Texture>>&, const ref<Texture>&)` | 从纹理列表创建 |
| `static ref<Fbo> create2D(ref<Device>, uint32_t w, uint32_t h, const Desc&, ...)` | 创建 2D 帧缓冲 |
| `static ref<Fbo> createCubemap(...)` | 创建立方体贴图帧缓冲 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `void attachColorTarget(const ref<Texture>&, uint32_t rtIndex, ...)` | 附加颜色纹理 |
| `void attachDepthStencilTarget(const ref<Texture>&, ...)` | 附加深度-模板纹理 |
| `ref<Texture> getColorTexture(uint32_t index) const` | 获取颜色附件纹理 |
| `const ref<Texture>& getDepthStencilTexture() const` | 获取深度-模板纹理 |
| `uint32_t getWidth/getHeight() const` | 获取帧缓冲尺寸 |
| `void setSamplePositions(...)` | 设置可编程采样位置 |
| `static uint32_t getMaxColorTargetCount()` | 获取最大颜色目标数量 |

## 依赖关系
### 本文件引用
- `Handles.h`, `Formats.h`, `ResourceViews.h`, `Texture.h`, `Device.h`

### 被以下文件引用
- `BlitContext.h`, `RenderContext.h`, `GraphicsStateObject.h`
