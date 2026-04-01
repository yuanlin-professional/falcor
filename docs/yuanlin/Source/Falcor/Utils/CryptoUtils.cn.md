# CryptoUtils 源码文档

> 路径: `Source/Falcor/Utils/CryptoUtils.h` + `Source/Falcor/Utils/CryptoUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供 SHA-1 哈希计算的工具类。支持增量式更新和一次性计算，可将任意数据或基本类型哈希为 20 字节的消息摘要。

## 类与接口

### `SHA1`

- **继承**: 无
- **职责**: 计算 SHA-1 哈希值

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SHA1()` | 构造函数，初始化哈希状态 |
| `void update(uint8_t value)` | 逐字节更新哈希 |
| `void update(const void* data, size_t len)` | 以数据块更新哈希 |
| `void update(const T& value)` | 更新基本类型值的哈希 |
| `void update(const std::string_view str)` | 更新字符串视图的哈希 |
| `MD finalize()` | 完成哈希计算，返回 20 字节消息摘要 |
| `static MD compute(const void* data, size_t len)` | 一次性计算数据的 SHA-1 哈希 |
| `static std::string toString(const MD& sha1)` | 将哈希转换为 40 字符十六进制字符串 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `MD` | `std::array<uint8_t, 20>` | 消息摘要类型别名 |
| `mIndex` | `uint32_t` | 缓冲区当前索引 |
| `mBits` | `uint64_t` | 已处理的总位数 |
| `mState[5]` | `uint32_t[5]` | SHA-1 内部状态 |
| `mBuf[64]` | `uint8_t[64]` | 64 字节处理块缓冲区 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义

### 被以下文件引用

- 资源缓存、场景哈希等需要计算数据指纹的模块

## 实现细节

- 完整实现了 SHA-1 算法的 80 轮计算
- 使用 16 个 32 位字的循环缓冲区进行消息调度
- `processBlock` 使用宏展开的方式实现四组不同的轮函数
- `finalize` 处理填充（0x80 后跟零填充和 64 位长度）
