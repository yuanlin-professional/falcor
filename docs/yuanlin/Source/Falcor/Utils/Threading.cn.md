# Threading 源码文档

> 路径: `Source/Falcor/Utils/Threading.h` + `Source/Falcor/Utils/Threading.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供全局线程池和线程同步工具。`Threading` 类管理一个固定大小的线程池，支持任务派发和等待。`Barrier` 类提供线程屏障同步原语，作为 C++20 `std::barrier` 的临时替代。

## 类与接口

### `Threading`

- **继承**: 无（静态类）
- **职责**: 管理全局线程池的生命周期和任务派发

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static void start(uint32_t threadCount = 16)` | 初始化全局线程池 |
| `static void finish()` | 等待所有当前线程完成 |
| `static void shutdown()` | 等待完成并关闭线程池 |
| `static uint32_t getLogicalThreadCount()` | 返回硬件支持的最大并发线程数 |
| `static Task dispatchTask(const function<void()>& func)` | 在可用线程上启动任务 |

### `Threading::Task`

- **职责**: 派发任务的句柄

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool isRunning()` | 检查任务是否仍在执行（未实现） |
| `void finish()` | 等待任务完成（未实现） |

### `Barrier`

- **继承**: 无
- **职责**: 线程屏障，所有线程到达后才能继续

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Barrier(size_t threadCount, function<void()> completionFunc)` | 构造屏障，指定线程数和可选完成回调 |
| `void wait()` | 等待所有线程到达屏障点 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mThreadCount` | `size_t` | 参与屏障的线程总数 |
| `mWaitCount` | `size_t` | 剩余等待线程数 |
| `mGeneration` | `size_t` | 代际标识，防止虚假唤醒 |
| `mCompletionFunc` | `function<void()>` | 所有线程到达后的回调 |

## 依赖关系

### 本文件引用

- `Core/Macros.h`、`Core/Error.h` — 宏和错误处理
- `<thread>`、`<mutex>`、`<condition_variable>` — 标准线程库

### 被以下文件引用

- 场景加载、纹理处理等需要多线程的模块

## 实现细节

- 线程池使用引用计数模式：`start` 增加计数，`shutdown` 减少计数，仅最后一个 `shutdown` 才实际关闭线程池
- `dispatchTask` 使用循环索引选择下一个线程槽，如果该线程仍在运行则先 `join`
- `Task::isRunning()` 和 `Task::finish()` 当前标记为 `FALCOR_UNIMPLEMENTED()`
- `Barrier` 使用代际（generation）机制防止虚假唤醒，支持可选的完成回调
