# LockFile 源码文档

> 路径: `Source/Falcor/Core/Platform/LockFile.h` + `Source/Falcor/Core/Platform/LockFile.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Platform

## 功能概述

提供跨平台的文件锁抽象封装。在 Windows 系统上使用 `LockFileEx()` / `UnlockFileEx()` 实现，在 POSIX (Linux) 系统上使用 `flock()` 实现。支持独占锁和共享锁两种模式，以及阻塞和非阻塞两种获取方式。

## 类与接口

### `LockFile`

- **继承**: 无
- **职责**: 封装操作系统级别的文件锁操作，提供统一的跨平台接口

#### 内部枚举

| 枚举类型 | 值 | 说明 |
|----------|------|------|
| `LockType::Exclusive` | - | 独占锁，同一时刻仅允许一个持有者 |
| `LockType::Shared` | - | 共享锁，允许多个持有者同时读取 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `LockFile(const std::filesystem::path& path)` | 构造函数，打开（或创建）指定路径的锁文件 |
| `bool open(const std::filesystem::path& path)` | 打开锁文件，文件不存在时自动创建，返回是否成功 |
| `void close()` | 关闭锁文件 |
| `bool isOpen() const` | 返回锁文件是否已打开 |
| `bool tryLock(LockType lockType = LockType::Exclusive)` | 非阻塞方式尝试获取锁，获取失败立即返回 false |
| `bool lock(LockType lockType = LockType::Exclusive)` | 阻塞方式获取锁，直到锁可用 |
| `bool unlock()` | 释放已持有的锁 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFileHandle` | `FileHandle` (Windows 下为 `void*`，Linux 下为 `int`) | 操作系统文件句柄 |
| `mIsOpen` | `bool` | 标记锁文件是否已打开 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 宏定义（`FALCOR_API`、`FALCOR_WINDOWS`、`FALCOR_LINUX`）
- `<filesystem>` — 文件路径
- `<windows.h>` — Windows 平台 API（`CreateFileW`、`LockFileEx`、`UnlockFileEx`）
- `<sys/file.h>` / `<unistd.h>` — Linux 平台 API（`flock`）

### 被以下文件引用
- 其他需要进程间文件锁同步的模块

## 实现细节

- 该类禁用了拷贝构造和赋值操作符，确保锁的唯一所有权。
- **Windows 实现**: `open()` 使用 `CreateFileW` 以 `FILE_FLAG_OVERLAPPED` 标志打开文件。`lock()` 阻塞模式通过 `OVERLAPPED` 事件和 `GetOverlappedResult` 实现等待。`tryLock()` 使用 `LOCKFILE_FAIL_IMMEDIATELY` 标志实现非阻塞尝试。
- **Linux 实现**: `open()` 使用 POSIX `open()` 以 `O_RDWR | O_CREAT` 打开文件。锁操作统一通过 `flock()` 系统调用实现，非阻塞模式使用 `LOCK_NB` 标志。
- 析构函数自动调用 `close()` 以确保资源正确释放。
