# Swapchain 源码文档

> 路径: `Source/Falcor/Core/API/Swapchain.h` / `Swapchain.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Swapchain 封装了交换链对象，管理窗口渲染目标的呈现。支持垂直同步、窗口遮挡检测、全屏模式切换和动态调整大小。

## 类与接口

### `Swapchain`
- **继承**: `Object`
- **职责**: 管理窗口交换链

#### 内部结构体 `Desc`
| 成员 | 类型 | 说明 |
|------|------|------|
| `format` | `ResourceFormat` | 后缓冲格式 |
| `width` / `height` | `uint32_t` | 后缓冲尺寸 |
| `imageCount` | `uint32_t` | 图像数量（默认 3） |
| `enableVSync` | `bool` | 启用垂直同步 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `const ref<Texture>& getImage(uint32_t index) const` | 获取后缓冲图像 |
| `void present()` | 呈现下一个图像 |
| `int acquireNextImage()` | 获取下一个可用图像索引 |
| `void resize(uint32_t width, uint32_t height)` | 调整大小 |
| `bool isOccluded()` | 检查窗口是否被遮挡 |
| `void setFullScreenMode(bool mode)` | 切换全屏模式 |

## 依赖关系
### 本文件引用
- `Texture.h`, `Formats.h`, `Core/Object.h`, `slang-gfx.h`

### 被以下文件引用
- 窗口管理和帧呈现相关模块
