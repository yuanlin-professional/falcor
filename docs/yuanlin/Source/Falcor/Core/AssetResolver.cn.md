# AssetResolver 源码文档

> 路径: `Source/Falcor/Core/AssetResolver.h` + `Source/Falcor/Core/AssetResolver.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

提供资产文件路径解析功能。`AssetResolver` 负责将资产的相对路径解析为绝对路径,支持按资产类别（场景、纹理等）配置独立的搜索路径列表。解析逻辑依次尝试绝对路径、工作目录相对路径和搜索目录相对路径。

## 枚举类型

### `AssetCategory`
资产类别枚举,用于区分不同类型的资产搜索路径。

| 值 | 说明 |
|------|------|
| `Any` | 任意类别（默认） |
| `Scene` | 场景文件 |
| `Texture` | 纹理文件 |
| `Count` | 类别计数 |

### `SearchPathPriority`
搜索路径优先级枚举。

| 值 | 说明 |
|------|------|
| `First` | 添加到搜索路径列表开头 |
| `Last` | 添加到搜索路径列表末尾 |

## 类与接口

### `AssetResolver`
- **职责**: 管理搜索路径并将资产相对路径解析为绝对路径

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `resolvePath(path, category)` | 将路径解析为已存在的绝对文件路径；解析失败返回空路径 |
| `resolvePathPattern(path, pattern, firstMatchOnly, category)` | 按正则表达式模式匹配搜索目录下的文件列表 |
| `addSearchPath(path, priority, category)` | 添加绝对搜索路径，可指定优先级和资产类别 |
| `getDefaultResolver()` | 获取全局默认资产解析器（静态方法） |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mSearchContexts` | `std::vector<SearchContext>` | 按资产类别存储的搜索上下文列表 |

### `SearchContext`（私有内部结构体）
- **职责**: 维护单个资产类别的搜索路径列表并执行路径解析

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 宏定义
- `Core/Enum.h` — 枚举注册工具
- `Core/Platform/OS.h` — 文件系统工具函数（`globFilesInDirectory`、`isSamePath`）
- `Utils/Scripting/ScriptBindings.h` — Python 脚本绑定

### 被以下文件引用
- `Core/SampleApp.cpp` — 初始化资产搜索路径
- `Core/Testbed.cpp` — 初始化资产搜索路径

## 实现细节

- 构造函数根据 `AssetCategory::Count` 初始化搜索上下文数组。
- `resolvePath` 先尝试将路径作为绝对路径或工作目录相对路径解析，失败后依次在指定类别和 `Any` 类别的搜索路径中查找。
- `addSearchPath` 会去重：若路径已存在则移动到指定优先级位置。
- 提供完整的 Python 绑定，可通过 `AssetResolver.default_resolver` 访问全局实例。
