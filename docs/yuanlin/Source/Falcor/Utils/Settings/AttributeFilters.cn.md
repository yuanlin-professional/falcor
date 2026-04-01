# AttributeFilters 源码文档

> 路径: `Source/Falcor/Utils/Settings/AttributeFilters.h` + `Source/Falcor/Utils/Settings/AttributeFilters.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Settings

## 功能概述

属性过滤器系统，用于基于正则表达式匹配的形状（shape）名称来管理和检索属性集合。支持通过 JSON 格式添加过滤规则，每条规则包含名称、正则表达式和属性字典。过滤器按添加顺序依次应用，后添加的规则会覆盖先前匹配的同名属性。同时兼容已废弃的 `.filter` 语法。

## 类与接口

### `AttributeFilter`

- **命名空间**: `Falcor::settings`
- **职责**: 管理一组基于正则表达式的属性过滤记录，根据形状名称匹配并返回对应的属性集合

#### 内部结构

| 结构体 | 说明 |
|--------|------|
| `Record` | 单条过滤记录，包含名称（`name`）、正则表达式（`regex`）和属性字典（`attributes`） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void add(const nlohmann::json& json)` | 添加 JSON 格式的过滤规则，内部调用 `addJson` |
| `void clear()` | 清空所有过滤记录 |
| `Attributes getAttributes(std::string_view shapeName) const` | 根据形状名称匹配所有规则，返回合并后的 `Attributes` 对象 |
| `std::optional<T> getAttribute(std::string_view shapeName, std::string_view attrName) const` | 模板方法，获取指定形状的指定属性值，支持类型检查和 bool/算术类型自动转换 |
| `T getAttribute(std::string_view shapeName, std::string_view attrName, const T& def) const` | 带默认值的属性获取，属性不存在时返回默认值 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void addJson(const nlohmann::json& json)` | 根据 JSON 类型分发到 `addArray` 或 `addDictionary` |
| `void addArray(const nlohmann::json& array)` | 遍历数组中每个元素，逐一调用 `addDictionary` |
| `void addDictionary(const nlohmann::json& dict)` | 解析字典中的 `name`、`regex`、`attributes` 字段，构建 `Record` 并添加到过滤列表 |
| `nlohmann::json processDeprecatedFilters(std::string_view name, nlohmann::json flattened, const std::string& regexStr)` | 处理已废弃的 `.filter` 语法，将其转换为标准过滤记录 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mAttributes` | `std::vector<Record>` | 存储所有过滤记录的有序列表 |

## 依赖关系

### 本文件引用

- `SettingsUtils.h` — 提供 `flattenDictionary` 和 `TypeChecker` 等工具
- `Attributes.h` — `Attributes` 类定义
- `Core/Macros.h` — Falcor 宏定义
- `Core/Error.h` — 错误检查宏 `FALCOR_CHECK`
- `Utils/Logger.h` — 日志工具
- `nlohmann/json.hpp` — JSON 解析库

### 被以下文件引用

- `Settings.h` — `Settings` 类内部使用 `AttributeFilter` 管理属性过滤

## 实现细节

- **正则匹配机制**: 每个 `Record` 包含一个编译好的 `std::regex`，`getAttributes` 和 `getAttribute` 遍历所有记录，对形状名称执行 `std::regex_match`，匹配成功则合并/覆盖属性。
- **属性展平**: 添加字典时通过 `detail::flattenDictionary` 将嵌套 JSON 展平为冒号分隔的键名格式（如 `"foo:bar"`）。
- **废弃语法兼容**: `processDeprecatedFilters` 识别以 `.filter` 结尾的键，将其拆分为独立的过滤记录。支持取反正则（`[regex, true]` 格式），取反时先对所有形状应用属性，再对匹配正则的形状移除该属性。
- **类型安全**: `getAttribute` 模板方法使用 `TypeChecker` 验证 JSON 值类型与请求类型匹配，不匹配时抛出 `TypeError` 异常。
