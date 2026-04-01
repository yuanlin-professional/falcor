# DefineList.h 源码文档

> 路径: `Source/Falcor/Core/Program/DefineList.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Core/Program

## 功能概述

提供着色器宏定义列表的管理功能。`DefineList` 是一个键值对容器，用于存储着色器编译时的预处理宏定义（`#define`），在着色器程序创建和编译流程中被广泛使用。

## 类与接口

### `DefineList`

- **继承**: `std::map<std::string, std::string>`
- **职责**: 管理着色器编译时的宏定义集合，支持链式调用的增删操作

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `DefineList& add(const std::string& name, const std::string& val = "")` | 添加单个宏定义，若已存在则替换 |
| `DefineList& remove(const std::string& name)` | 移除单个宏定义，若不存在则静默忽略 |
| `DefineList& add(const DefineList& dl)` | 批量添加另一个宏定义列表中的所有条目 |
| `DefineList& remove(const DefineList& dl)` | 批量移除另一个宏定义列表中的所有条目 |
| `DefineList(std::initializer_list<...> il)` | 支持初始化列表构造 |

#### 关键成员

继承自 `std::map<std::string, std::string>`，键为宏名称，值为宏的值（可为空字符串）。

## 依赖关系

### 本文件引用

- `<initializer_list>` — 初始化列表支持
- `<map>` — 基类容器
- `<string>` — 键值类型

### 被以下文件引用

- `Program.h` / `Program.cpp` — 用于管理程序的宏定义
- `ProgramManager.h` / `ProgramManager.cpp` — 全局宏定义管理
- `ProgramVersion.h` — 存储特定版本的宏定义
- `ShaderVar.h` — 间接依赖

## 实现细节

- 所有方法均为内联实现，返回自身引用以支持链式调用模式（如 `defines.add("A", "1").add("B", "2")`）
- 继承 `std::map` 使其自动具备排序和比较能力，便于作为 `ProgramVersionKey` 的一部分进行版本缓存查找
- `remove` 操作对不存在的键不会抛出异常，保证了调用安全性
