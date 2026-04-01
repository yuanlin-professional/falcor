# BinaryFileStream.h 源码文档

> 路径: `Source/Falcor/Utils/BinaryFileStream.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供二进制文件读写的辅助类。封装了 `std::fstream`，简化了二进制文件的打开、读取、写入、跳过及关闭等操作，并支持模板化的流操作符。

## 类与接口

### `BinaryFileStream`

- **继承**: 无
- **职责**: 管理二进制文件的 I/O 操作，支持读、写和读写模式

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BinaryFileStream(const path& path, Mode mode)` | 构造并打开文件 |
| `void open(const path& path, Mode mode)` | 打开文件流 |
| `void close()` | 关闭文件流 |
| `void skip(uint32_t count)` | 跳过指定字节数 |
| `void remove()` | 关闭并删除文件 |
| `uint32_t getRemainingStreamSize()` | 获取流中剩余字节数 |
| `bool isGood()` | 检查流是否正常 |
| `bool isBad()` | 检查是否发生 I/O 错误 |
| `bool isFail()` | 检查是否有任何错误 |
| `bool isEof()` | 检查是否到达文件末尾 |
| `BinaryFileStream& read(void* pData, size_t count)` | 从流中读取数据 |
| `BinaryFileStream& write(const void* pData, size_t count)` | 向流中写入数据 |
| `operator>>(T& val)` | 模板化流提取运算符 |
| `operator<<(const T& val)` | 模板化流插入运算符 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mStream` | `std::fstream` | 底层文件流 |
| `mPath` | `std::filesystem::path` | 文件路径 |

### `BinaryFileStream::Mode` 枚举

| 值 | 说明 |
|----|------|
| `Read` | 只读模式 |
| `Write` | 只写模式 |
| `ReadWrite` | 读写模式 |

## 依赖关系

### 本文件引用

- `<filesystem>` — 文件路径
- `<fstream>` — 文件流

### 被以下文件引用

- 场景缓存、资源加载等需要二进制文件读写的模块

## 实现细节

- 析构函数自动调用 `close()` 关闭流
- `read` 和 `write` 返回自身引用以支持链式调用
