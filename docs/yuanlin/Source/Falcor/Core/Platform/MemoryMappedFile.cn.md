# MemoryMappedFile 源码文档

> 路径: `Source/Falcor/Core/Platform/MemoryMappedFile.h` + `Source/Falcor/Core/Platform/MemoryMappedFile.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Platform

## 功能概述

提供跨平台的内存映射文件读取工具类。将文件内容直接映射到进程的虚拟地址空间中，避免显式的读取操作，从而提高大文件的读取效率。支持 Windows（`CreateFileMapping` / `MapViewOfFile`）和 Linux（`mmap`）两个平台。

## 类与接口

### `MemoryMappedFile`

- **继承**: 无
- **职责**: 封装操作系统的内存映射文件 API，提供统一的只读文件映射接口

#### 内部枚举

| 枚举类型 | 值 | 说明 |
|----------|------|------|
| `AccessHint::Normal` | - | 通用访问模式，均衡性能 |
| `AccessHint::SequentialScan` | - | 顺序扫描模式，适合从头到尾读取 |
| `AccessHint::RandomAccess` | - | 随机访问模式，适合随机位置读取 |

#### 常量

| 常量 | 类型 | 说明 |
|------|------|------|
| `kWholeFile` | `size_t` | `std::numeric_limits<size_t>::max()`，表示映射整个文件 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `MemoryMappedFile(const std::filesystem::path& path, size_t mappedSize, AccessHint accessHint)` | 构造并打开文件映射 |
| `bool open(const std::filesystem::path& path, size_t mappedSize, AccessHint accessHint)` | 打开并映射文件，返回是否成功 |
| `void close()` | 关闭映射和文件 |
| `bool isOpen() const` | 返回文件是否已映射 |
| `size_t getSize() const` | 获取文件总大小（字节） |
| `const void* getData() const` | 获取映射数据指针 |
| `size_t getMappedSize() const` | 获取当前映射区域大小（字节） |
| `static size_t getPageSize()` | 获取操作系统内存页大小 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `bool remap(uint64_t offset, size_t mappedSize)` | 替换当前映射为同一文件的新区域，偏移量必须为页大小的整数倍 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mPath` | `std::filesystem::path` | 文件路径 |
| `mAccessHint` | `AccessHint` | 访问模式提示 |
| `mSize` | `size_t` | 文件总大小 |
| `mFile` | `FileHandle` | 操作系统文件句柄 |
| `mMappedFile` | `void*` | Windows 下的文件映射对象句柄 |
| `mMappedData` | `void*` | 映射后的内存数据指针 |
| `mMappedSize` | `size_t` | 当前映射区域大小 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 宏定义
- `<windows.h>` — Windows API（`CreateFile`、`CreateFileMapping`、`MapViewOfFile`）
- `<sys/mman.h>` / `<fcntl.h>` — Linux API（`mmap64`、`munmap`、`madvise`）

### 被以下文件引用
- 需要高效读取大文件的模块（如场景加载、纹理加载等）

## 实现细节

- 禁用了拷贝构造和赋值操作符，保证映射资源的唯一所有权。
- `open()` 若已经打开则返回 false，不会重复映射。
- 映射大小自动裁剪到文件实际大小。
- **Windows**: 通过 `FILE_ATTRIBUTE_NORMAL` / `FILE_FLAG_SEQUENTIAL_SCAN` / `FILE_FLAG_RANDOM_ACCESS` 设置访问提示。
- **Linux**: 通过 `madvise()` 的 `MADV_NORMAL` / `MADV_SEQUENTIAL` / `MADV_RANDOM` 设置访问提示。
- `remap()` 方法允许在不关闭底层文件的情况下重新映射不同偏移的区域。
- `getPageSize()` 在 Windows 上返回 `dwAllocationGranularity`，在 Linux 上返回 `_SC_PAGESIZE`。
