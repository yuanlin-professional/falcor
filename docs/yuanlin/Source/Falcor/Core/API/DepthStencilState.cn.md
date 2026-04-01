# DepthStencilState 源码文档

> 路径: `Source/Falcor/Core/API/DepthStencilState.h` / `DepthStencilState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

DepthStencilState 封装了深度-模板测试状态，控制深度测试、深度写入、模板测试及模板操作等图形管线功能。

## 类与接口

### `DepthStencilState`
- **继承**: `Object`
- **职责**: 管理深度-模板状态的创建和查询

#### 内部枚举
| 枚举 | 说明 |
|------|------|
| `Face` | 面朝向选择（Front、Back、FrontAndBack） |
| `StencilOp` | 模板操作（Keep、Zero、Replace、Increase、IncreaseSaturate、Decrease、DecreaseSaturate、Invert） |

#### 内部结构体 `StencilDesc`
| 成员 | 说明 |
|------|------|
| `func` | 模板比较函数 |
| `stencilFailOp` | 模板测试失败时的操作 |
| `depthFailOp` | 模板通过但深度失败时的操作 |
| `depthStencilPassOp` | 两者都通过时的操作 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<DepthStencilState> create(const Desc& desc)` | 创建深度-模板状态 |
| `bool isDepthTestEnabled() const` | 查询深度测试是否启用 |
| `bool isDepthWriteEnabled() const` | 查询深度写入是否启用 |
| `ComparisonFunc getDepthFunc() const` | 获取深度比较函数 |
| `bool isStencilTestEnabled() const` | 查询模板测试是否启用 |
| `const StencilDesc& getStencilDesc(Face face) const` | 获取指定面的模板描述 |

## 依赖关系
### 本文件引用
- `Types.h`, `Handles.h`, `Core/Macros.h`, `Core/Object.h`

### 被以下文件引用
- `GraphicsStateObject.h`（作为图形管线状态的一部分）
