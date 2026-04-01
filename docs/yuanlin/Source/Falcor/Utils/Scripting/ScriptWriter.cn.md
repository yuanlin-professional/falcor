# ScriptWriter 源码文档

> 路径: `Source/Falcor/Utils/Scripting/ScriptWriter.h`
> 类型: C++ 头文件（仅头文件实现）
> 模块: Utils/Scripting

## 功能概述

提供一个辅助工具类，用于以编程方式生成 Python 脚本代码字符串。支持生成函数调用、成员函数调用、属性获取/设置等 Python 代码片段。C++ 类型的参数会通过 `ScriptBindings::repr()` 自动转换为对应的 Python 代码表示。

## 类与接口

### `ScriptWriter`

- **继承**: 无
- **职责**: 提供静态方法，将 C++ 类型的函数调用和属性操作序列化为合法的 Python 脚本代码字符串。

#### 内部结构体

| 结构体 | 说明 |
|--------|------|
| `VariableName` | 包装一个变量名字符串，使其在生成代码时作为标识符而非字符串字面量输出 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static std::string makeFunc(const std::string& func)` | 生成无参函数调用代码，如 `func()\n` |
| `static std::string makeFunc(const std::string& func, Arg first, Args... args)` | 生成带参数的函数调用代码，参数通过 `getArgString` 转换为字符串 |
| `static std::string makeMemberFunc(const std::string& var, const std::string& func)` | 生成无参成员函数调用代码，如 `var.func()\n` |
| `static std::string makeMemberFunc(const std::string& var, const std::string& func, Arg first, Args... args)` | 生成带参数的成员函数调用代码 |
| `static std::string makeGetProperty(const std::string& var, const std::string& property)` | 生成属性获取代码，如 `var.property\n` |
| `static std::string makeSetProperty(const std::string& var, const std::string& property, Arg arg)` | 生成属性设置代码，如 `var.property = value\n` |
| `static std::string getPathString(const std::filesystem::path& path)` | 将文件路径转换为使用正斜杠的字符串格式 |
| `template<typename T> static std::string getArgString(const T& arg)` | 将参数转换为 Python 代码字符串（默认通过 `ScriptBindings::repr`） |

#### `getArgString` 模板特化

| 特化类型 | 说明 |
|----------|------|
| `pybind11::dict` | 直接将 Python 字典转换为字符串表示 |
| `ScriptWriter::VariableName` | 直接输出变量名，不加引号 |

## 依赖关系

### 本文件引用

- `ScriptBindings.h` — `ScriptBindings::repr()` 函数
- `Core/Platform/OS.h` — 平台抽象
- `<filesystem>` — 文件路径处理
- `<algorithm>` — `std::replace`

### 被以下文件引用

- 渲染管线中需要序列化场景配置为 Python 脚本的模块

## 实现细节

- 使用可变参数模板和折叠表达式实现多参数函数调用的代码生成。
- `getArgString` 默认实现调用 `ScriptBindings::repr()` 将 C++ 值转换为其 Python 表示。
- `VariableName` 类型用于区分"需要引号的字符串值"和"不需要引号的变量引用"。
- 路径处理会将 Windows 反斜杠自动替换为正斜杠，以兼容 Python 路径语法。
