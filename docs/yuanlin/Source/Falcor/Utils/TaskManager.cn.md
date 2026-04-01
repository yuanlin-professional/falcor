# TaskManager 源码文档

> 路径: `Source/Falcor/Utils/TaskManager.h` + `Source/Falcor/Utils/TaskManager.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供 CPU/GPU 混合任务管理器。支持将 CPU 任务提交到线程池并行执行，同时管理需要在主线程上串行执行的 GPU 任务。CPU 任务和 GPU 任务可交叉提交，`finish` 方法等待所有任务完成。

## 类与接口

### `TaskManager`

- **继承**: 无
- **职责**: 管理 CPU 并行任务和 GPU 串行任务的调度与同步

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TaskManager(bool startPaused = false)` | 构造函数，可选暂停启动 |
| `void addTask(CpuTask&& task)` | 添加 CPU 任务，未暂停时立即开始执行 |
| `void addTask(GpuTask&& task)` | 添加 GPU 任务，仅在 `finish` 调用时执行 |
| `void finish(RenderContext* renderContext)` | 取消暂停并等待所有任务完成 |

#### 类型别名

| 名称 | 类型 | 说明 |
|------|------|------|
| `CpuTask` | `std::function<void()>` | CPU 任务类型 |
| `GpuTask` | `std::function<void(RenderContext*)>` | GPU 任务类型 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mThreadPool` | `BS::thread_pool` | 底层线程池 |
| `mCurrentlyRunning` | `atomic_size_t` | 当前正在执行的任务数 |
| `mCurrentlyScheduled` | `atomic_size_t` | 当前已调度但未开始的任务数 |
| `mGpuTasks` | `vector<GpuTask>` | 待执行的 GPU 任务队列 |
| `mException` | `exception_ptr` | 存储的异常 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义
- `BS_thread_pool/BS_thread_pool.hpp` — 第三方线程池库

### 被以下文件引用

- 场景加载等需要并行处理的模块

## 实现细节

- CPU 任务在线程池中并行执行，异常被捕获并存储
- GPU 任务在 `finish` 的主线程循环中串行执行
- `finish` 使用条件变量等待：有 GPU 任务时执行，有运行中任务时等待，全部完成时退出
- 最后一个正在运行的 CPU 任务完成时通过条件变量通知主线程
- 任何存储的异常在 `finish` 结束时重新抛出
