# Dictionary.h 源码文档

> 路径: `Source/Falcor/Utils/Dictionary.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供一个通用字典容器类，基于 `std::unordered_map` 和 `std::any` 实现，允许以字符串为键存储任意类型的值。

## 类与接口

### `Dictionary`

- **继承**: 无
- **职责**: 提供类型安全的键值存储，支持通过隐式类型转换存取值

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Value& operator[](const std::string& key)` | 通过键访问或创建值 |
| `const Value& operator[](const std::string& key) const` | 通过键只读访问值 |
| `size_t size() const` | 获取字典大小 |
| `bool keyExists(const std::string& key) const` | 检查键是否存在 |
| `T getValue(const std::string& key)` | 获取指定键的值，不存在则抛出异常 |
| `T getValue(const std::string& key, const T& defaultValue)` | 获取指定键的值，不存在则返回默认值 |
| `begin()` / `end()` | 迭代器支持 |

### `Dictionary::Value`

- **职责**: 封装 `std::any`，提供模板化赋值和类型转换运算符

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mContainer` | `std::unordered_map<std::string, Value>` | 底层键值存储 |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 错误检查
- `<any>` — 任意类型存储

### 被以下文件引用

- 渲染通道配置等需要灵活键值存储的模块

## 实现细节

- 值通过 `std::any_cast` 进行类型转换，类型不匹配时抛出异常
- 支持拷贝构造
