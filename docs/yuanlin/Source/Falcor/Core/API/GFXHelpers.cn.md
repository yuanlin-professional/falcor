# GFXHelpers 源码文档

> 路径: `Source/Falcor/Core/API/GFXHelpers.h` / `GFXHelpers.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

GFXHelpers 提供了 Falcor 内部类型与 GFX（Slang 图形抽象层）类型之间的转换辅助函数，包括资源格式和资源状态的映射。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `gfx::Format getGFXFormat(ResourceFormat format)` | 将 Falcor `ResourceFormat` 转换为 GFX 格式 |
| `gfx::ResourceState getGFXResourceState(Resource::State state)` | 将 Falcor 资源状态转换为 GFX 资源状态 |
| `void getGFXResourceState(ResourceBindFlags, gfx::ResourceState&, gfx::ResourceStateSet&)` | 根据绑定标志计算默认状态和允许的状态集 |

## 依赖关系
### 本文件引用
- `Formats.h`, `Resource.h`, `slang-gfx.h`

### 被以下文件引用
- `Buffer.cpp`, `Texture.cpp`, `CopyContext.cpp`, `Device.cpp` 等
