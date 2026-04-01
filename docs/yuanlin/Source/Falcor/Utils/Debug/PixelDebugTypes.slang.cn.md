# PixelDebugTypes.slang 源码文档
> 路径: `Source/Falcor/Utils/Debug/PixelDebugTypes.slang`
> 类型: Slang 着色器文件（主机/设备共享类型定义）
> 模块: Utils/Debug

## 功能概述

定义像素调试系统中 CPU 和 GPU 共享的数据类型，包括打印值类型枚举、打印记录结构体和断言记录结构体。这些类型同时被 C++ 主机端（`PixelDebug.h`）和 Slang 着色器端（`PixelDebug.slang`）引用，确保数据布局一致。

## 结构体与接口

### `PrintValueType`（枚举）

定义 `print()` 支持的基本值类型。

| 枚举值 | 数值 | 说明 |
|--------|------|------|
| `Bool` | 0 | 布尔类型 |
| `Int` | 1 | 有符号整数类型 |
| `Uint` | 2 | 无符号整数类型 |
| `Float` | 3 | 浮点数类型 |

### `PrintRecord`（结构体）

单条打印记录，存储一次 `print()` 调用的完整信息。

| 成员 | 类型 | 说明 |
|------|------|------|
| `msgHash` | `uint` | print 消息的字符串哈希值 |
| `type` | `uint` | 值类型（对应 `PrintValueType` 枚举） |
| `count` | `uint` | 分量数量（1-4） |
| `_pad0` | `uint` | 填充字节，确保对齐 |
| `data` | `uint4` | 数据位。编码方式由 `type` 字段决定 |

### `AssertRecord`（结构体）

单条断言记录，存储一次 `assert()` 失败的信息。

| 成员 | 类型 | 说明 |
|------|------|------|
| `launchIndex` | `uint3` | 断言触发的启动索引（像素坐标） |
| `msgHash` | `uint` | assert 消息的字符串哈希值 |

## 依赖关系

### import

- `Utils/HostDeviceShared.slangh` — 提供 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 宏，用于在 C++ 和 Slang 之间共享命名空间

### 被以下文件引用

- `PixelDebug.h` — C++ 主机端通过 `#include` 引用
- `PixelDebug.slang` — Slang 着色器端通过 `import PixelDebugTypes` 引用

## 实现细节

- **跨语言共享**: 使用 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 宏以及 `#pragma once` 保护，使得同一文件既可被 C++ 编译器 `#include`，也可被 Slang 编译器 `import`。
- **内存对齐**: `PrintRecord` 设计为 32 字节（8 个 `uint`），满足 16 字节对齐要求。C++ 端有静态断言 `sizeof(PrintRecord) % 16 == 0` 进行验证。
- **`AssertRecord`** 为 16 字节（4 个 `uint`），天然满足对齐要求。
