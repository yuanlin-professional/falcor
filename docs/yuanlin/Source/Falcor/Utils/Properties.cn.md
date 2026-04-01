# Properties 源码文档

> 路径: `Source/Falcor/Utils/Properties.h` + `Source/Falcor/Utils/Properties.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

通用属性存储类，以有序 JSON 对象为后端。支持多种基本类型和向量类型的存取，可与 JSON 文件和 Python 字典之间相互转换。提供序列化/反序列化框架（`PropertiesWriter`/`PropertiesReader`），支持自定义类型和枚举类型的自动序列化。

## 类与接口

### `Properties`

- **继承**: 无
- **职责**: 存储和管理有序键值属性，支持 JSON 和 Python 互操作

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Properties()` | 默认构造（空对象） |
| `Properties(const json& j)` | 从 JSON 构造 |
| `Properties(const pybind11::dict& d)` | 从 Python 字典构造 |
| `json toJson() const` | 转换为 JSON |
| `pybind11::dict toPython() const` | 转换为 Python 字典 |
| `string dump(int indent) const` | 序列化为字符串 |
| `bool empty() const` | 检查是否为空 |
| `bool has(string_view name) const` | 检查属性是否存在 |
| `void set<T>(string_view name, const T& value)` | 设置属性值 |
| `T get<T>(string_view name) const` | 获取属性值（不存在时抛出异常） |
| `T get<T>(string_view name, const T& def) const` | 获取属性值（不存在时返回默认值） |
| `bool getTo<T>(string_view name, T& value) const` | 获取属性值到引用（返回是否存在） |
| `optional<T> getOpt<T>(string_view name) const` | 获取可选属性值 |
| `Value operator[]` / `ConstValue operator[]` | 下标访问器 |
| `Iterator begin()` / `Iterator end()` | 迭代器 |

### `PropertiesWriter`

- **职责**: 将对象序列化为 `Properties`

### `PropertiesReader`

- **职责**: 从 `Properties` 反序列化对象

### 支持的类型

`bool`、`int32_t`、`int64_t`、`uint32_t`、`uint64_t`、`float`、`double`、`string`、`path`、`int2/3/4`、`uint2/3/4`、`float2/3/4`、`Properties`（嵌套）

## 依赖关系

### 本文件引用

- `nlohmann/json.hpp` — JSON 库
- `pybind11` — Python 绑定
- `Core/Enum.h` — 枚举序列化支持
- `Utils/Math/VectorTypes.h` — 向量类型

### 被以下文件引用

- 渲染通道、材质系统等需要配置属性的模块

## 实现细节

- 使用 `nlohmann::ordered_json` 保持属性插入顺序
- 向量类型以 JSON 数组形式存储
- 枚举类型自动通过 `enumToString`/`stringToEnum` 转换为字符串
- 带 `serialize` 成员函数模板的自定义类型自动通过 `PropertiesWriter`/`PropertiesReader` 处理
- Python 到 JSON 转换支持 None、bool、int、float、str、list、tuple、dict、path 及向量类型
- 使用 pimpl 模式隐藏 JSON 头文件依赖，减少编译时间
- 提供 `fmt::formatter` 特化以支持格式化输出
