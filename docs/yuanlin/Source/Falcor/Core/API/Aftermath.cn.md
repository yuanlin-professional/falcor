# Aftermath 源码文档

> 路径: `Source/Falcor/Core/API/Aftermath.h` / `Aftermath.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Aftermath 模块封装了 NVIDIA NSight Aftermath SDK，用于在 GPU 崩溃时生成崩溃转储文件（`.nv-gpudmp`）。该功能仅在 `FALCOR_HAS_AFTERMATH` 编译标志启用时可用。Aftermath 能够捕获 GPU 崩溃现场信息，包括着色器调试信息、命令列表标记等，对调试 GPU 崩溃问题非常有用。

## 类与接口

### `AftermathFlags`（枚举）
- **职责**: 定义 Aftermath 功能标志

| 枚举值 | 说明 |
|--------|------|
| `Minimum` | 最小功能集 |
| `EnableMarkers` | 启用命令列表标记（需要 Aftermath Monitor 运行） |
| `EnableResourceTracking` | 启用资源追踪 |
| `CallStackCapturing` | 捕获调用栈 |
| `GenerateShaderDebugInfo` | 生成着色器调试信息 |
| `EnableShaderErrorReporting` | 启用着色器错误报告 |
| `Defaults` | 默认启用所有上述标志 |

### `AftermathContext`
- **职责**: 每设备的 Aftermath 上下文，管理 Aftermath 的初始化和标记添加

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `AftermathContext(Device* pDevice)` | 构造函数，关联设备 |
| `bool initialize(AftermathFlags flags)` | 在设备上初始化 Aftermath，支持 D3D12（Vulkan 仅支持基础崩溃转储） |
| `void addMarker(const LowLevelContextData*, std::string_view name)` | 向命令列表添加事件标记 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `Device*` | 关联的设备指针 |
| `mInitialized` | `bool` | 是否已初始化 |
| `mContextHandle` | `int32_t` | Aftermath 上下文句柄 |

### 自由函数
| 函数签名 | 说明 |
|----------|------|
| `void enableAftermath()` | 全局启用 GPU 崩溃转储追踪 |
| `void disableAftermath()` | 全局禁用 GPU 崩溃转储追踪 |
| `bool waitForAftermathDumps(int timeout = 5)` | 等待崩溃转储生成完成 |

## 实现细节

- 使用全局静态 `sMutex` 保护线程安全
- 崩溃转储回调 `gpuCrashDumpCallback` 将转储写入 `.nv-gpudmp` 文件和对应的 JSON 文件
- 着色器调试信息缓存在 `sShaderDebugInfo` 映射表中，用于崩溃转储解码
- `addMarker` 使用 `GFSDK_Aftermath_SetEventMarker` 在 D3D12 命令列表上设置标记
- 崩溃转储描述中包含应用名称 "Falcor" 和版本字符串

## 依赖关系
### 本文件引用
- `Device.h`, `LowLevelContextData.h`, `NativeHandle.h`, `NativeHandleTraits.h`
- `Core/Macros.h`, `Core/Version.h`, `Utils/Logger.h`
- NVIDIA Aftermath SDK 头文件（`GFSDK_Aftermath.h` 等）

### 被以下文件引用
- `CopyContext.cpp`（通过 `addAftermathMarker` 调用）
- `Device.h`/`Device.cpp`（持有 `AftermathContext` 实例）
