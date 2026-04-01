# Object 源码文档

> 路径: `Source/Falcor/Core/Object.h` + `Source/Falcor/Core/Object.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

实现 Falcor 框架的引用计数对象系统。提供 `Object` 基类作为所有引用计数对象的根类，以及 `ref<T>` 智能指针模板和 `BreakableReference<T>` 可断开引用模板。相比 `std::shared_ptr`，该系统具有更小的内存开销（32 位引用计数 + 单指针引用）和内置的引用跟踪调试支持。

## 类与接口

### `Object`
- **职责**: 所有引用计数对象的基类，维护原子引用计数器

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `getClassName()` | 返回类名（虚函数，配合 `FALCOR_OBJECT` 宏使用） |
| `refCount()` | 返回当前引用计数 |
| `incRef()` | 增加引用计数 |
| `decRef(dealloc)` | 减少引用计数，计数归零时可选释放对象 |
| `dumpAliveObjects()` | （调试）转储所有存活对象（需启用 `FALCOR_ENABLE_OBJECT_TRACKING`） |
| `dumpRefs()` | （调试）转储本对象的所有引用信息 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mRefCount` | `std::atomic<uint32_t>` | 原子引用计数器 |

### `ref<T>`
- **职责**: 侵入式智能指针，自动管理 `Object` 派生对象的引用计数

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `ref(T2* ptr)` | 从裸指针构造（显式） |
| `reset(T2* ptr)` | 重置为新指针 |
| `get()` | 获取裸指针 |
| `swap(ref& r)` | 交换两个引用 |
| `operator->()` / `operator*()` | 解引用操作符 |
| `operator bool()` | 空指针检查 |

### `BreakableReference<T>`
- **职责**: 可断开的强引用，用于打破引用循环

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `breakStrongReference()` | 释放强引用，之后通过弱指针访问对象 |
| `get()` | 获取裸指针（始终通过弱指针） |

## 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `make_ref<T>(args...)` | 创建 `ref<T>` 并构造 T 对象 |
| `static_ref_cast<T>(ref<U>)` | 静态类型转换 |
| `dynamic_ref_cast<T>(ref<U>)` | 动态类型转换 |

## 宏定义

| 宏 | 说明 |
|------|------|
| `FALCOR_OBJECT(class_)` | 在类声明中重写 `getClassName()` 返回实际类名 |
| `FALCOR_ENABLE_OBJECT_TRACKING` | 编译开关：启用对象生命周期跟踪（默认关闭） |
| `FALCOR_ENABLE_REF_TRACKING` | 编译开关：启用引用跟踪（需同时启用对象跟踪） |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — `FALCOR_API` 导出宏
- `Core/Error.h` — 错误报告（`reportFatalErrorAndTerminate`）
- `fmt/core.h` — 格式化

### 被以下文件引用
- 框架中所有继承 `Object` 的类（`Device`、`Texture`、`Buffer` 等数百个类）
- `Core/ObjectPython.h` — Python 绑定适配

## 实现细节

- `incRef` / `decRef` 使用 `std::atomic::fetch_add/fetch_sub` 保证线程安全。
- 引用计数降到 0 时检测下溢并报告致命错误。
- 对象跟踪通过全局 `std::set<const Object*>` 实现，受互斥锁保护。
- 引用跟踪通过每对象的 `std::map<uint64_t, RefTracker>` 存储每个引用的堆栈跟踪信息。
- 提供 `fmt::formatter` 特化和 `std::hash` 特化，使 `ref<T>` 可用于格式化输出和哈希容器。
