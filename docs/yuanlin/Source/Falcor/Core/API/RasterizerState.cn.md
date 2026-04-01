# RasterizerState 源码文档

> 路径: `Source/Falcor/Core/API/RasterizerState.h` / `RasterizerState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

RasterizerState 封装了光栅化阶段的状态配置，包括剔除模式、填充模式、深度偏移、深度裁剪、线抗锯齿、裁剪测试和保守光栅化等功能。

## 类与接口

### `RasterizerState`
- **继承**: `Object`
- **职责**: 管理光栅化状态

#### 内部枚举
| 枚举 | 说明 |
|------|------|
| `CullMode` | 剔除模式（None、Front、Back） |
| `FillMode` | 填充模式（Wireframe、Solid） |

#### 内部类 `Desc`（建造者模式）
| 方法 | 说明 |
|------|------|
| `setCullMode(CullMode)` | 设置剔除模式 |
| `setFillMode(FillMode)` | 设置填充模式 |
| `setFrontCounterCW(bool)` | 设置正面朝向（逆时针/顺时针） |
| `setDepthBias(int32_t, float)` | 设置深度偏移 |
| `setDepthClamp(bool)` | 启用深度裁剪 |
| `setLineAntiAliasing(bool)` | 启用线抗锯齿 |
| `setScissorTest(bool)` | 启用裁剪测试 |
| `setConservativeRasterization(bool)` | 启用保守光栅化 |
| `setForcedSampleCount(uint32_t)` | 设置强制采样数 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<RasterizerState> create(const Desc&)` | 创建光栅化状态 |
| 各种 getter 方法 | 查询状态参数 |

## 依赖关系
### 本文件引用
- `Handles.h`, `Core/Macros.h`, `Core/Object.h`, `Core/Enum.h`

### 被以下文件引用
- `GraphicsStateObject.h`
