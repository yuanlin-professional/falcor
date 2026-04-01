# ImageLoader 源码文档

> 路径: `Source/RenderPasses/ImageLoader/ImageLoader.h` + `ImageLoader.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/ImageLoader

## 功能概述

图像加载渲染通道，将图像文件加载为纹理并输出到渲染图中。支持多种图像格式，可配置输出尺寸（默认/固定为原始大小）、sRGB 加载、MipMap 生成、数组切片和 Mip 级别选择。当输出尺寸与原始图像不同时，使用双线性缩放。

## 类与接口

### `ImageLoader`

- **继承**: `RenderPass`
- **职责**: 从文件加载图像纹理并输出到渲染图

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ImageLoader(ref<Device>, const Properties&)` | 构造函数，解析属性并加载图像 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输出 `dst`，尺寸根据模式决定 |
| `void compile(RenderContext*, const CompileData&)` | 编译时检查图像已加载 |
| `void execute(RenderContext*, const RenderData&)` | 将图像 blit 到输出纹理 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI（输出尺寸、文件加载、sRGB、MipMap、预览） |
| `Properties getProperties() const` | 序列化属性 |
| `bool loadImage(const filesystem::path&)` | 加载图像文件 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mOutputSizeSelection` | `RenderPassHelpers::IOSize` | 输出尺寸模式（Default/Fixed） |
| `mOutputFormat` | `ResourceFormat` | 输出格式 |
| `mOutputSize` | `uint2` | 当前输出尺寸 |
| `mpTex` | `ref<Texture>` | 已加载的纹理 |
| `mImagePath` | `filesystem::path` | 图像文件路径 |
| `mArraySlice` | `uint32_t` | 数组切片索引 |
| `mMipLevel` | `uint32_t` | Mip 级别 |
| `mGenerateMips` | `bool` | 是否生成 MipMap |
| `mLoadSRGB` | `bool` | 是否以 sRGB 格式加载 |

## 依赖关系

### 本文件引用

- `Falcor.h`、`RenderGraph/RenderPass.h`、`RenderGraph/RenderPassHelpers.h`
- `Core/AssetResolver.h` — 资源路径解析

### 被以下文件引用

- 渲染图系统通过插件注册机制加载

## 实现细节

- 输出尺寸模式：`Default` 由连接的通道决定；`Fixed` 使用图像原始尺寸
- `execute` 通过 `blit` 将指定 Mip 和数组切片复制到输出纹理，支持自动缩放
- UI 中图像尺寸变化且模式为 `Fixed` 时触发渲染图重新编译
- 图像路径通过 `AssetResolver` 解析，支持搜索路径机制
- 无需场景即可独立工作
