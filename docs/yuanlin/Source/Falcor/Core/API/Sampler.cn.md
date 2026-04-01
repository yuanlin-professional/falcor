# Sampler 源码文档

> 路径: `Source/Falcor/Core/API/Sampler.h` / `Sampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Sampler 封装了纹理采样器状态对象，控制纹理采样时的过滤模式、寻址模式、LOD 参数、比较函数、缩减模式和边界颜色等属性。

## 类与接口

### 相关枚举
| 枚举 | 说明 |
|------|------|
| `TextureFilteringMode` | 过滤模式（Point、Linear） |
| `TextureAddressingMode` | 寻址模式（Wrap、Mirror、Clamp、Border、MirrorOnce） |
| `TextureReductionMode` | 缩减模式（Standard、Comparison、Min、Max） |

### `Sampler`
- **继承**: `Object`
- **职责**: 管理纹理采样器状态

#### 内部结构体 `Desc`（建造者模式）
| 方法 | 说明 |
|------|------|
| `setFilterMode(min, mag, mip)` | 设置过滤模式 |
| `setMaxAnisotropy(uint32_t)` | 设置最大各向异性 |
| `setLodParams(minLod, maxLod, lodBias)` | 设置 LOD 参数 |
| `setComparisonFunc(ComparisonFunc)` | 设置比较函数 |
| `setReductionMode(TextureReductionMode)` | 设置缩减模式 |
| `setAddressingMode(modeU, modeV, modeW)` | 设置寻址模式 |
| `setBorderColor(const float4&)` | 设置边界颜色 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `gfx::ISamplerState* getGfxSamplerState() const` | 获取 GFX 采样器状态 |
| `NativeHandle getNativeHandle() const` | 获取原生句柄 |
| `const Desc& getDesc() const` | 获取描述符 |
| 各种 getter | 查询过滤模式、寻址模式、LOD 等参数 |

## 依赖关系
### 本文件引用
- `Types.h`, `Handles.h`, `NativeHandle.h`, `Core/Object.h`, `Core/Enum.h`

### 被以下文件引用
- `BlitContext.h`, `RenderContext.h`, `ParameterBlock.h`, `Device.h`
