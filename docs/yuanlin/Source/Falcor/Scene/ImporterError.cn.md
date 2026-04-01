# ImporterError 源码文档

> 路径: `Source/Falcor/Scene/ImporterError.h`
> 类型: C++ 头文件
> 模块: Scene

## 功能概述

定义场景导入过程中抛出的异常类 `ImporterError`。该类从 `Importer.h` 中分离出来，以避免引入 pybind11 相关类型（通过 SceneBuilder）。异常对象携带导入资产的文件路径和错误描述信息。

## 类与接口

### `ImporterError`

- **继承**: `Falcor::Exception`
- **职责**: 表示场景导入期间发生的异常，记录出错的资产路径

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ImporterError(const path& path, string_view what)` | 构造异常，携带文件路径和错误信息 |
| `ImporterError(const path& path, fmt::format_string<Args...> format, Args&&... args)` | 模板构造函数，支持 fmt 格式化错误信息 |
| `const path& path() const noexcept` | 获取导致异常的资产文件路径 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpPath` | `shared_ptr<filesystem::path>` | 导入资产的文件路径 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` - 基础宏定义
- `Core/Error.h` - 基类 `Exception`
- `Core/Platform/OS.h` - 操作系统平台抽象

### 被以下文件引用

- `Importer.h` - 导入器基类
- 各具体导入器实现
