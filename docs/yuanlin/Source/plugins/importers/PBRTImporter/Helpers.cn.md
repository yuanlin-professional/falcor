# Helpers 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/Helpers.h`
> 类型: C++ 头文件（仅头文件）
> 模块: plugins/importers/PBRTImporter

## 功能概述

pbrt 导入器的错误处理和日志辅助函数。将 pbrt 文件位置信息附加到错误/警告消息中。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void throwError(fmt::format_string<Args...>, Args&&...)` | 抛出格式化异常（不含位置信息） |
| `void throwError(const FileLoc&, fmt::format_string<Args...>, Args&&...)` | 抛出带文件位置的格式化异常 |
| `void logWarning(const FileLoc&, fmt::format_string<Args...>, Args&&...)` | 输出带文件位置的格式化警告 |

## 依赖关系

### 本文件引用

- `Types.h` - `FileLoc`
- `Core/Error.h` - `FALCOR_THROW`
- `Utils/Logger.h` - `logWarning`

### 被以下文件引用

- `Builder.cpp` - 场景构建器错误处理
- `PBRTImporter.cpp` - 导入器错误处理
