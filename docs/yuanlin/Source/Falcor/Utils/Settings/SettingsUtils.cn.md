# SettingsUtils 源码文档

> 路径: `Source/Falcor/Utils/Settings/SettingsUtils.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils/Settings

## 功能概述

设置系统的底层工具集，提供 JSON 字典展平、JSON 值类型检查和类型错误异常类。这些工具被 `Attributes` 和 `AttributeFilters` 等上层类广泛使用。

## 类与接口

### 命名空间 `Falcor::settings::detail`

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `nlohmann::json flattenDictionary(const nlohmann::json& dict)` | 将嵌套 JSON 字典展平为冒号分隔的键名格式，例如 `{"foo":{"bar":4}}` 变为 `{"foo:bar":4}` |
| `void flattenDictionary(const nlohmann::json& dict, const std::string& prefix, nlohmann::json& flattened)` | 递归辅助函数，将嵌套字典逐级展平到输出对象中 |
| `bool isType<T>(const nlohmann::json& json)` | 模板函数，检查 JSON 值是否为数值/布尔类型（算术类型特化） |
| `bool isType<T>(const nlohmann::json& json)` | 模板函数，检查 JSON 值是否为字符串类型（`std::string` 特化） |
| `bool isType<T, U, N>(const nlohmann::json& json)` | 模板函数，检查 JSON 值是否为指定大小和元素类型的数组（`std::array` 特化） |

### `TypeChecker<T>`

- **命名空间**: `Falcor::settings::detail`
- **职责**: 类型检查器模板，验证 JSON 值与请求的 C++ 类型是否兼容

| 特化 | 说明 |
|------|------|
| 通用 `TypeChecker<T>` | 调用 `isType<T>` 进行类型检查 |
| `TypeChecker<std::array<U, N>>` | 针对 `std::array` 的特化，检查数组长度和元素类型 |

### `TypeError`

- **继承**: `std::runtime_error`
- **职责**: 当属性类型与请求类型不匹配时抛出的异常

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义（`FALCOR_ASSERT`）
- `Core/Error.h` — 错误处理
- `nlohmann/json.hpp` — JSON 库
- `fmt/format.h` — 格式化库

### 被以下文件引用

- `Attributes.h` — 使用 `TypeChecker` 和 `TypeError`
- `AttributeFilters.h` — 使用 `TypeChecker`、`TypeError` 和 `flattenDictionary`
- `Settings.cpp` — 使用 `flattenDictionary`

## 实现细节

- **展平算法**: 递归遍历 JSON 对象，对非对象值直接以前缀为键写入结果；对对象值则拼接冒号分隔的路径继续递归。空前缀时不添加冒号前缀。
- **类型检查宽松性**: 算术类型的 `isType` 同时接受 `is_number()` 和 `is_boolean()`，允许布尔值和数值之间的隐式转换。
- **GCC 兼容**: `TypeChecker` 模板使用额外的 `gccfix` 模板参数来规避 GCC 编译器在非命名空间作用域中禁止显式特化的 bug。
