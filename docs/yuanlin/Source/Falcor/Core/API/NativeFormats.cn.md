# NativeFormats 源码文档

> 路径: `Source/Falcor/Core/API/NativeFormats.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

NativeFormats.h 提供了 DXGI 格式到 Falcor ResourceFormat 以及 Vulkan VkFormat 到 Falcor ResourceFormat 的转换映射。在没有 `<dxgiformat.h>` 头文件的环境下（如 Linux），本文件会自行定义 `DXGI_FORMAT` 枚举和 `VkFormat` 枚举的副本。

## 转换函数

| 函数 | 说明 |
|------|------|
| `getDxgiFormat(ResourceFormat)` | Falcor 格式转 DXGI 格式 |
| `getResourceFormat(DXGI_FORMAT)` | DXGI 格式转 Falcor 格式 |
| `getVulkanFormat(ResourceFormat)` | Falcor 格式转 Vulkan 格式 |

## 依赖关系
### 本文件引用
- `Formats.h`, `Core/Macros.h`

### 被以下文件引用
- 需要在 Falcor 格式与原生 API 格式之间转换的模块
