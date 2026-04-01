# BlendState 源码文档

> 路径: `Source/Falcor/Core/API/BlendState.h` / `BlendState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

BlendState 封装了图形管线的混合状态，控制片段着色器输出与渲染目标之间的颜色混合方式。支持独立混合模式（每个渲染目标单独配置）、Alpha-to-Coverage 以及颜色写入掩码等功能。

## 类与接口

### `BlendState`
- **继承**: `Object`
- **职责**: 管理混合状态的创建和查询

#### 内部枚举

| 枚举 | 说明 |
|------|------|
| `BlendOp` | 混合运算（Add、Subtract、ReverseSubtract、Min、Max） |
| `BlendFunc` | 混合因子（Zero、One、SrcColor、DstColor、SrcAlpha 等共 17 种） |

#### 内部类 `Desc`
- **职责**: 混合状态描述符，使用建造者模式配置

| 方法签名 | 说明 |
|----------|------|
| `Desc& setBlendFactor(const float4& factor)` | 设置常量混合因子 |
| `Desc& setIndependentBlend(bool enabled)` | 启用/禁用独立混合模式 |
| `Desc& setRtParams(uint32_t rtIndex, ...)` | 设置指定渲染目标的混合参数 |
| `Desc& setRtBlend(uint32_t rtIndex, bool enable)` | 启用/禁用指定渲染目标的混合 |
| `Desc& setAlphaToCoverage(bool enabled)` | 启用/禁用 Alpha-to-Coverage |
| `Desc& setRenderTargetWriteMask(...)` | 设置颜色写入掩码 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<BlendState> create(const Desc& desc)` | 创建混合状态对象 |
| `const float4& getBlendFactor() const` | 获取常量混合因子 |
| `BlendOp getRgbBlendOp(uint32_t rtIndex) const` | 获取 RGB 混合运算 |
| `bool isBlendEnabled(uint32_t rtIndex) const` | 查询混合是否启用 |
| `bool isAlphaToCoverageEnabled() const` | 查询 Alpha-to-Coverage 是否启用 |

## 依赖关系
### 本文件引用
- `Handles.h`, `Core/Macros.h`, `Core/Object.h`, `Utils/Math/Vector.h`
- `FBO.h`（初始化渲染目标数量）

### 被以下文件引用
- `GraphicsStateObject.h`（作为图形管线状态的一部分）
