# ScriptBindings 源码文档

> 路径: `Source/Falcor/Utils/Scripting/ScriptBindings.h` + `Source/Falcor/Utils/Scripting/ScriptBindings.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Scripting

## 功能概述

提供 Falcor 与 Python 之间的脚本绑定基础设施。该模块基于 pybind11 库实现，负责管理 Python 绑定的注册、延迟初始化和运行时动态绑定。同时提供了向量类型（bool/int/uint/float 的 2/3/4 分量向量）、矩阵类型以及 float16_t 类型到 Python 的完整映射，包括算术运算符、序列化（pickle）支持和隐式类型转换。

## 类与接口

### 命名空间 `Falcor::ScriptBindings`

- **职责**: 提供脚本绑定的注册与管理机制。

#### 关键类型

| 类型 | 说明 |
|------|------|
| `RegisterBindingFunc` | 绑定注册回调函数类型，签名为 `std::function<void(pybind11::module& m)>` |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void initModule(pybind11::module& m)` | 初始化绑定模块。执行所有延迟绑定，注册向量/矩阵类型，并保存模块引用以支持运行时绑定 |
| `void registerBinding(RegisterBindingFunc f)` | 注册脚本绑定函数。若脚本系统已初始化则立即执行，否则延迟注册 |
| `void registerDeferredBinding(const std::string& name, RegisterBindingFunc f)` | 注册延迟脚本绑定，在脚本系统初始化前使用。名称必须唯一 |
| `void resolveDeferredBinding(const std::string& name, pybind11::module& m)` | 按名称解析并执行延迟绑定，用于控制绑定的执行顺序 |

#### 辅助模板函数

| 方法签名 | 说明 |
|----------|------|
| `template<typename T> void addEnumBinaryOperators(pybind11::enum_<T>& e)` | 为 Python 枚举添加位与（`&`）和位或（`|`）运算符，使枚举可用作标志位 |
| `template<typename T> std::string repr(const T& value)` | 返回已注册类型值的 Python 字符串表示 |

### `DeferredBinding`（匿名命名空间内部结构）

- **职责**: 存储延迟绑定的名称、回调函数和注册状态。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `std::string` | 绑定的名称标识 |
| `bindingFunc` | `RegisterBindingFunc` | 绑定注册回调 |
| `isRegistered` | `bool` | 是否已执行绑定 |

### `pybind11::falcor_enum<T>`

- **继承**: `pybind11::enum_<T>`
- **职责**: 自动从 `Falcor::EnumInfo<T>` 读取枚举项信息并注册到 Python，处理 Python 保留关键字冲突（如 `None` 映射为 `None_`）。

### `pybind11::detail::type_caster<Falcor::float16_t>`

- **职责**: 实现 `float16_t` 与 Python `float` 之间的自动类型转换。

### 宏定义

| 宏 | 说明 |
|----|------|
| `FALCOR_SCRIPT_BINDING(_name)` | 声明一个脚本绑定函数并自动注册为延迟绑定。不可在静态库中使用 |
| `FALCOR_SCRIPT_BINDING_DEPENDENCY(_name)` | 声明对指定绑定的依赖，确保在当前绑定之前执行 |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 错误处理
- `Core/Enum.h` — 枚举信息支持（`EnumInfo`、`has_enum_info_v`）
- `Core/ObjectPython.h` — Python 对象支持
- `Core/Plugin.h` — 插件管理（`PluginManager`）
- `Utils/Math/Vector.h` — 向量类型定义
- `Utils/Math/Matrix.h` — 矩阵类型定义
- `<pybind11/*>` — pybind11 绑定库
- `<fmt/format.h>` — 字符串格式化

### 被以下文件引用

- `Utils/Scripting/Scripting.h` — 脚本引擎
- `Utils/Scripting/ScriptWriter.h` — 脚本编写器
- 所有使用 `FALCOR_SCRIPT_BINDING` 宏的模块

## 实现细节

- `initModule` 在 pybind11 模块初始化时被调用，注册所有 Falcor 基础类型到 Python。
- 支持的向量类型包括：`bool2/3/4`、`int2/3/4`、`uint2/3/4`、`float2/3/4`、`float16_t2/3/4`。
- 支持的矩阵类型包括：`float3x3`、`float3x4`、`float4x4`（仅注册为数据类型，无运算符）。
- `defineVecType` 模板函数为每种向量类型注册构造器、分量访问器、字符串表示、pickle 序列化以及可选的算术运算符。
- 向量类型支持从 Python 列表的隐式转换（通过 `std::array` 中间类型）。
- 浮点向量额外支持从整数列表的隐式转换。
- `float16_t` 和 `float` 向量之间支持显式转换。
- 模块通过 `atexit` 注册清理回调，在 Python 退出时自动释放模块引用。
- 提供 `load_plugin` / `loadPlugin` 函数用于从 Python 加载 Falcor 插件。
