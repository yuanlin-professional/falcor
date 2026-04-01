# Attributes 源码文档

> 路径: `Source/Falcor/Utils/Settings/Attributes.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils/Settings

## 功能概述

属性容器类，封装了一个 JSON 字典来存储和检索键值对属性。提供类型安全的属性获取、合并、清除和删除操作。属性名称使用冒号分隔的展平格式（如 `"foo:bar:baz"`）。

## 类与接口

### `Attributes`

- **命名空间**: `Falcor::settings`
- **职责**: 存储键值对属性集合，提供类型安全的读取和修改接口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Attributes()` | 默认构造函数 |
| `Attributes(nlohmann::json jsonDict)` | 从 JSON 字典构造 |
| `void overrideWith(const Attributes& other)` | 用另一个 `Attributes` 覆盖当前属性 |
| `std::optional<T> get(std::string_view attrName) const` | 模板方法，按名称获取属性值，类型不匹配抛出 `TypeError`，不存在返回空 |
| `T get(std::string_view attrName, const T& def) const` | 带默认值的属性获取 |
| `bool has(std::string_view attrName) const` | 检查属性是否存在 |
| `void addDict(nlohmann::json jsonDict)` | 合并 JSON 字典到当前属性，同名键被覆盖 |
| `void clear()` | 清空所有属性 |
| `void removePrefix(std::string_view prefix)` | 移除所有以指定前缀开头的属性 |
| `void removeExact(std::string_view name)` | 精确移除指定名称的属性 |
| `std::string to_string() const` | 将属性序列化为 JSON 字符串 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mJsonDict` | `nlohmann::json` | 内部 JSON 字典，存储所有属性键值对 |

## 依赖关系

### 本文件引用

- `SettingsUtils.h` — 提供 `TypeChecker` 类型检查工具和 `TypeError` 异常
- `Core/Macros.h` — Falcor 宏定义
- `Core/Error.h` — 错误处理
- `nlohmann/json.hpp` — JSON 库

### 被以下文件引用

- `AttributeFilters.h` — `AttributeFilter::getAttributes` 返回 `Attributes` 对象
- `Settings.h` — `Settings` 类使用 `Attributes` 作为 `Options` 和 `Attributes` 的类型别名

## 实现细节

- **类型转换**: `get<T>` 方法支持 `bool` 和算术类型之间的隐式转换。当请求 `bool` 类型但 JSON 中存储的是整数时，非零值转为 `true`；反之请求算术类型但 JSON 中存储的是 `bool` 时，`true` 转为 `T(1)`，`false` 转为 `T(0)`。
- **全部内联实现**: 所有方法均在头文件中内联定义，无对应的 `.cpp` 文件。
