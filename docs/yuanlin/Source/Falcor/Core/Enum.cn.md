# Enum 源码文档

> 路径: `Source/Falcor/Core/Enum.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Core

## 功能概述

提供枚举类型与字符串之间的双向转换基础设施。通过 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 宏注册枚举信息后，即可使用模板函数在枚举值和字符串之间进行转换。同时支持位标志（flags）枚举的字符串列表转换。

## 模板工具

### 类型特征
| 名称 | 说明 |
|------|------|
| `EnumInfo<T>` | 通过 ADL 查找枚举信息类型 |
| `has_enum_info<T>` | 编译期检测枚举是否已注册信息 |
| `has_enum_info_v<T>` | `has_enum_info` 的便捷变量模板 |

### 转换函数
| 函数签名 | 说明 |
|----------|------|
| `enumToString<T>(T value)` | 枚举值转字符串；值未注册时抛出异常 |
| `stringToEnum<T>(string_view name)` | 字符串转枚举值；名称未注册时抛出异常 |
| `enumHasValue<T>(string_view name)` | 检查枚举是否包含指定名称的值 |
| `flagsToStringList<T>(T flags)` | 位标志枚举转字符串列表 |
| `stringListToFlags<T>(vector<string>)` | 字符串列表转位标志枚举值 |

## 宏定义

| 宏 | 说明 |
|------|------|
| `FALCOR_ENUM_INFO(T, ...)` | 定义枚举 `T` 的名称-值映射信息 |
| `FALCOR_ENUM_REGISTER(T)` | 通过 ADL 注册枚举信息，使其可被转换函数发现 |

## 依赖关系

### 本文件引用
- `Core/Error.h` — `FALCOR_THROW` 异常宏
- `Utils/Logger.h` — 日志
- `fstd/span.h` — span 容器
- `fmt/core.h` — 格式化库

### 被以下文件引用
- `Core/AssetResolver.h` — 资产类别枚举注册
- 框架中所有使用 `FALCOR_ENUM_INFO` / `FALCOR_ENUM_REGISTER` 的文件

## 实现细节

- 基于 ADL（参数依赖查找）机制，枚举信息可定义在任意命名空间中，只要在枚举所在命名空间注册即可被发现。
- 提供 `fmt::formatter` 特化，使已注册枚举可直接用于 `fmt::format` 格式化输出。
- 枚举信息以 `std::pair<T, std::string>` 数组的形式存储在静态局部变量中。
