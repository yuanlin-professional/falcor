# StringFormatters.h 源码文档

> 路径: `Source/Falcor/Utils/StringFormatters.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

为 `fmt` 格式化库提供自定义类型的格式化器（formatter）特化。当前支持 `std::filesystem::path` 和 `std::optional<T>` 两种类型的格式化输出。

## 类与接口

### `fmt::formatter<std::filesystem::path>`

- **继承**: `fmt::formatter<std::string>`
- **职责**: 将文件系统路径格式化为字符串

### `fmt::formatter<std::optional<T>>`

- **继承**: `fmt::formatter<T>`
- **职责**: 格式化可选值；有值时格式化内部值，无值时输出 `"nullopt"`

## 依赖关系

### 本文件引用

- `<fmt/format.h>` — 格式化库
- `<filesystem>` — 文件路径
- `<optional>` — 可选值

### 被以下文件引用

- `Logger.h` — 日志格式化
- 其他需要格式化 `path` 或 `optional` 的模块

## 实现细节

- `path` 格式化通过调用 `path.string()` 转换为字符串
- `optional` 格式化将已有值委托给基础类型的格式化器
