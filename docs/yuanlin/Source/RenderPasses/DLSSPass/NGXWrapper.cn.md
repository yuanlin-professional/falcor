# NGXWrapper 源码文档

> 路径: `Source/RenderPasses/DLSSPass/NGXWrapper.h` + `NGXWrapper.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DLSSPass

## 功能概述

本文件封装了 NVIDIA NGX SDK 中与 DLSS 相关的功能调用。`NGXWrapper` 类提供了 NGX 上下文初始化/关闭、DLSS 功能创建/释放、最优设置查询和 DLSS 评估执行等接口。支持 D3D12 和 Vulkan 两种图形 API 后端。

## 类与接口

### `NGXWrapper`（位于 `Falcor` 命名空间）

- **继承**: 无
- **职责**: 封装 NVIDIA NGX SDK 的 DLSS 功能接口

#### 嵌套结构体

##### `OptimalSettings`

| 成员 | 类型 | 说明 |
|------|------|------|
| `sharpness` | `float` | 推荐的锐化值 |
| `optimalRenderSize` | `uint2` | 最优渲染分辨率 |
| `minRenderSize` | `uint2` | 最小渲染分辨率 |
| `maxRenderSize` | `uint2` | 最大渲染分辨率 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `NGXWrapper(ref<Device>, const path&, const path&)` | 构造函数，初始化 NGX 上下文。失败时抛出异常 |
| `~NGXWrapper()` | 析构函数，关闭 NGX |
| `OptimalSettings queryOptimalSettings(uint2, NVSDK_NGX_PerfQuality_Value) const` | 查询给定分辨率和质量配置下的最优 DLSS 设置 |
| `void initializeDLSS(RenderContext*, uint2, uint2, Texture*, bool, bool, PerfQuality)` | 创建 DLSS 功能实例 |
| `void releaseDLSS()` | 释放 DLSS 功能实例 |
| `bool isDLSSInitialized() const` | 检查 DLSS 是否已初始化 |
| `bool evaluateDLSS(RenderContext*, Texture*, Texture*, Texture*, Texture*, Texture*, bool, float, float2, float2) const` | 执行 DLSS 评估（降噪+超分辨率） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | 图形设备引用 |
| `mInitialized` | `bool` | NGX 是否已初始化 |
| `mpParameters` | `NVSDK_NGX_Parameter*` | NGX 参数对象 |
| `mpFeature` | `NVSDK_NGX_Handle*` | DLSS 功能句柄 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心框架
- `Core/API/NativeHandleTraits.h` — 原生句柄特性
- `Core/API/NativeFormats.h` — 原生格式转换
- `<nvsdk_ngx_defs.h>` — NGX 定义
- `<nvsdk_ngx.h>` / `<nvsdk_ngx_helpers.h>` — D3D12 NGX API
- `<nvsdk_ngx_vk.h>` / `<nvsdk_ngx_helpers_vk.h>` — Vulkan NGX API

### 被以下文件引用

- `DLSSPass.h` — DLSS 渲染通道

## 实现细节

1. **多后端支持**: 通过 `FALCOR_HAS_D3D12` 和 `FALCOR_HAS_VULKAN` 编译宏条件编译，同时支持 D3D12 和 Vulkan 后端。根据 `mpDevice->getType()` 在运行时选择对应的 API 调用。

2. **NGX 初始化**: 使用应用 ID（`kAppID = 231313132`）和功能搜索路径初始化 NGX。初始化后检查 DLSS 可用性和驱动版本要求。

3. **DLSS 功能创建**: 设置低分辨率运动向量标志（`MVLowRes`），根据配置启用 HDR 和深度反转标志。通过 `NGX_D3D12_CREATE_DLSS_EXT` 或 `NGX_VULKAN_CREATE_DLSS_EXT` 创建功能。

4. **DLSS 评估**: 在执行前设置资源屏障，传入颜色、深度、运动向量、曝光等参数。评估后执行 UAV 屏障并提交命令缓冲区。

5. **Vulkan 支持**: 针对 Vulkan 后端，提供了 `getAspectMaskFromFormat` 辅助函数来正确设置深度/模板格式的 aspect mask，并使用 `NVSDK_NGX_Resource_VK` 包装纹理资源。

6. **锐度范围**: 查询最优设置后将锐度值钳制到 [-1, 1] 范围，以兼容不同版本的 DLSS DLL。
