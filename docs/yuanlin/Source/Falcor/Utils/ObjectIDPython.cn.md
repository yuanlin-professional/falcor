# ObjectIDPython.h 源码文档

> 路径: `Source/Falcor/Utils/ObjectIDPython.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

为 `ObjectID` 模板类提供 pybind11 类型转换器（type caster），使其能够在 Python 和 C++ 之间自动转换。Python 端将 `ObjectID` 表示为普通整数。

## 类与接口

### `pybind11::detail::type_caster<Falcor::ObjectID<...>>`

- **继承**: pybind11 类型转换器框架
- **职责**: 实现 `ObjectID` 与 Python 整数之间的双向转换

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool load(handle src, bool)` | 从 Python 对象加载为 C++ `ObjectID`，无效 ID 保持为 `Invalid()` |
| `static handle cast(const ObjectID& src, ...)` | 将 C++ `ObjectID` 转换为 Python 整数 |

## 依赖关系

### 本文件引用

- `ObjectID.h` — `ObjectID` 定义
- `<pybind11/pybind11.h>` — Python 绑定框架

### 被以下文件引用

- 需要在 Python 中使用强类型 ID 的绑定模块

## 实现细节

- 使用 `PyNumber_Long` 和 `PyLong_AsUnsignedLong` 进行 Python/C++ 数值转换
- 当 Python 整数等于 `kInvalidID` 时，转换为默认构造的无效 `ObjectID`
