# RenderPassHelpers 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderPassHelpers.h` + `RenderPassHelpers.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

提供渲染通道开发的辅助工具集，包括 I/O 尺寸计算、通道描述元数据结构、以及便捷的反射注册和资源清理函数。这些工具简化了渲染通道中输入/输出资源的声明和管理流程。

## 类与接口

### `RenderPassHelpers`

- **继承**: 无（结构体）
- **职责**: 提供渲染通道 I/O 尺寸的枚举和计算功能。

#### IOSize 枚举

| 枚举值 | 说明 |
|--------|------|
| `Default` | 使用默认大小（绑定的资源大小，系统默认为窗口大小） |
| `Fixed` | 使用固定像素大小 |
| `Full` | 使用完整窗口大小 |
| `Half` | 使用窗口大小的一半 |
| `Quarter` | 使用窗口大小的四分之一 |
| `Double` | 使用窗口大小的两倍 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static uint2 calculateIOSize(IOSize selection, const uint2 fixedSize, const uint2 windowSize)` | 根据选择模式计算实际 I/O 尺寸（像素） |

### `ChannelDesc`（结构体）

- **职责**: 描述渲染通道单个输入/输出通道的元数据。

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `std::string` | 渲染通道 I/O 引脚名称 |
| `texname` | `std::string` | 着色器中对应资源的名称（空表示非着色器变量） |
| `desc` | `std::string` | 人类可读的数据描述 |
| `optional` | `bool` | 是否为可选资源 |
| `format` | `ResourceFormat` | 默认格式（`Unknown` 表示由系统决定） |

### `ChannelList`

`std::vector<ChannelDesc>` 的类型别名，表示通道描述列表。

## 全局辅助函数

| 函数签名 | 说明 |
|----------|------|
| `DefineList getValidResourceDefines(const ChannelList&, const RenderData&, const std::string& prefix)` | 为可选通道生成着色器预处理宏定义（`is_valid_<texname>` = 0/1） |
| `void addRenderPassInputs(RenderPassReflection&, const ChannelList&, ResourceBindFlags, const uint2 dim)` | 批量注册输入通道到反射对象 |
| `void addRenderPassOutputs(RenderPassReflection&, const ChannelList&, ResourceBindFlags, const uint2 dim)` | 批量注册输出通道到反射对象 |
| `void clearRenderPassChannels(RenderContext*, const ChannelList&, const RenderData&)` | 清除所有可用通道的纹理资源（整数格式用 `uint4(0)`，浮点格式用 `float4(0)` |

## 依赖关系

### 本文件引用

- `RenderPass.h` - 渲染通道基类
- `RenderPassReflection.h` - 反射系统
- `Core/API/Formats.h` - 资源格式
- `Core/API/RenderContext.h` - 渲染上下文
- `Core/Program/Program.h` - 着色器宏定义

### 被以下文件引用

- 绝大多数具体渲染通道实现

## 实现细节

- **着色器宏生成**: `getValidResourceDefines()` 为每个可选通道生成 `#define is_valid_<texname> 1/0`，使着色器能够在编译时判断可选资源是否可用。
- **默认绑定标志**: 输入通道默认绑定标志为 `ShaderResource`，输出通道默认为 `UnorderedAccess`。
- **UAV 清除**: `clearRenderPassChannels()` 根据纹理格式选择 `clearUAV` 的整数或浮点变体，确保正确清除。
- **I/O 尺寸计算**: `calculateIOSize()` 对 `Default` 模式返回 `{0,0}`（表示不关心，由系统决定），其他模式按窗口大小倍率计算。
