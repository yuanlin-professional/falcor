# ObjectPython 源码文档

> 路径: `Source/Falcor/Core/ObjectPython.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Core

## 功能概述

将 Falcor 的 `ref<T>` 智能指针注册为 pybind11 的持有者类型（holder type）。这使得 Python 绑定层能够正确管理继承自 `Object` 的 C++ 对象的生命周期。

## 关键声明

```cpp
PYBIND11_DECLARE_HOLDER_TYPE(T, Falcor::ref<T>, true);
```

此宏告知 pybind11：
- 使用 `Falcor::ref<T>` 作为 T 类型对象的持有者
- 第三个参数 `true` 表示支持从裸指针隐式构造

## 依赖关系

### 本文件引用
- `Core/Object.h` — `ref<T>` 模板定义
- `pybind11/pybind11.h` — Python 绑定框架

### 被以下文件引用
- `Core/Window.cpp` — Python 绑定中使用 `ref<Window>`
- `Core/Testbed.cpp` — Python 绑定中使用 `ref<Testbed>`
- 所有需要将 `ref<T>` 导出到 Python 的模块
