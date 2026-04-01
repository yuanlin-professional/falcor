# GFXAPI 源码文档

> 路径: `Source/Falcor/Core/API/GFXAPI.h` / `GFXAPI.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

GFXAPI 模块提供了 GFX（Slang 图形抽象层）和 D3D12 的错误检查宏以及错误报告函数。这是 Falcor 与底层图形 API 交互的基础设施层。

## 宏定义

| 宏 | 说明 |
|----|------|
| `FALCOR_GFX_CALL(call)` | 检查 GFX 调用结果，失败时报告错误 |
| `FALCOR_D3D_CALL(call)` | （D3D12）检查 HRESULT，失败时报告错误 |
| `FALCOR_GET_COM_INTERFACE(base, type, var)` | （D3D12）QueryInterface 辅助宏 |
| `FALCOR_MAKE_SMART_COM_PTR(a)` | （D3D12）创建 COM 智能指针类型 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void gfxReportError(const char* api, const char* call, gfx::Result result)` | 报告 GFX/D3D 错误，抛出 RuntimeError 异常 |

## 依赖关系
### 本文件引用
- `Handles.h`, `Core/Macros.h`, `Utils/Logger.h`
- Slang 头文件（`slang.h`, `slang-gfx.h`, `slang-com-ptr.h`）

### 被以下文件引用
- 所有需要调用 GFX API 的文件
