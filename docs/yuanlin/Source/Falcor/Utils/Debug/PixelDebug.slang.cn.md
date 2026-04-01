# PixelDebug.slang 源码文档
> 路径: `Source/Falcor/Utils/Debug/PixelDebug.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Debug

## 功能概述

实现像素调试的 GPU 端逻辑。提供 `print()` 和 `assert()` 函数供着色器代码调用，用于在选定像素处输出调试值或验证条件。通过 `_PIXEL_DEBUG_ENABLED` 宏控制功能的启用与禁用——禁用时所有函数为空操作，不产生性能开销。

支持打印 `bool`、`int`、`uint`、`float` 及其向量类型（最多 4 分量）。

## 结构体与接口

### `PixelDebug`（结构体）

核心调试数据结构，通过 `ParameterBlock` 从主机端绑定。

| 成员 | 类型 | 说明 |
|------|------|------|
| `counterBuffer` | `RWByteAddressBuffer` | 原子计数器缓冲区（偏移 0: print 计数，偏移 4: assert 计数） |
| `printBuffer` | `RWStructuredBuffer<PrintRecord>` | print 记录缓冲区 |
| `assertBuffer` | `RWStructuredBuffer<AssertRecord>` | assert 记录缓冲区 |
| `printBufferCapacity` | `uint` | print 缓冲区容量 |
| `assertBufferCapacity` | `uint` | assert 缓冲区容量 |
| `selectedPixel` | `uint2` | 当前选中的调试像素坐标 |

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `void print(uint2 pixel, String msg, PrintValueType valueType, int count, uint4 data)` | 若 `pixel` 等于 `selectedPixel`，通过原子操作写入一条 print 记录。 |
| `void assert(uint2 pixel, String msg)` | 通过原子操作写入一条 assert 记录，记录像素坐标和消息哈希。 |

### `IPrintable`（接口）

可打印值的通用接口，所有支持 `print()` 的类型必须实现此接口。

| 方法签名 | 说明 |
|----------|------|
| `uint getPrintableDataVal()` | 将值转换为 `uint` 位表示 |
| `static PrintValueType getPrintableValueType()` | 返回该类型对应的 `PrintValueType` 枚举值 |

### 类型扩展（extension）

以下基础类型通过 `extension` 实现了 `IPrintable` 接口：

| 类型 | `PrintValueType` | 转换方式 |
|------|-------------------|----------|
| `bool` | `Bool` | `uint(this)` |
| `int` | `Int` | `asuint(this)` |
| `uint` | `Uint` | 直接返回 |
| `float` | `Float` | `asuint(this)` |

## 函数

### 全局函数

| 函数签名 | 说明 |
|----------|------|
| `void printSetPixel(uint2 pixel)` | 设置当前像素坐标，后续 `print()`/`assert()` 将使用此坐标。启用时写入全局变量 `gPixelDebugPixel`。 |
| `void print<T : IPrintable>(String msg, T v)` | 打印单个标量值。启用时写入缓冲区，禁用时为空操作。 |
| `void print<T : IPrintable, let N : int>(String msg, vector<T, N> v)` | 打印向量值（最多 4 分量）。启用时写入缓冲区，禁用时为空操作。 |
| `void assert(bool condition, String msg = "")` | 断言条件为真。条件为假时记录当前像素坐标。启用时写入缓冲区，禁用时为空操作。 |

### 全局变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gPixelDebug` | `ParameterBlock<PixelDebug>` | 主机端绑定的调试参数块实例 |
| `gPixelDebugPixel` | `uint2` | 当前像素坐标（由 `printSetPixel()` 设置） |

## 依赖关系

### import

- `PixelDebugTypes` — 提供 `PrintRecord`、`AssertRecord`、`PrintValueType` 等共享类型定义

### 被以下文件引用

- 任何需要像素调试功能的着色器程序（通过 `import PixelDebug` 引入）

## 实现细节

- **条件编译**: 所有调试逻辑通过 `#ifdef _PIXEL_DEBUG_ENABLED` 保护，禁用时定义空函数。
- **原子操作**: 使用 `InterlockedAdd` 在计数器缓冲区上进行原子递增，确保多线程安全。print 使用偏移 0，assert 使用偏移 4。
- **容量保护**: 写入前检查索引是否超出缓冲区容量，防止越界写入。
- **泛型机制**: 使用 Slang 泛型 `<T : IPrintable>` 和 `<T : IPrintable, let N : int>` 实现对标量和向量类型的统一处理。
- **字符串哈希**: 使用 Slang 内置 `getStringHash(msg)` 获取字符串哈希值，避免在 GPU 端传递字符串。
- **已知问题**: 注释中提到由于 Slang 编译器 bug，`print()` 方法中未使用 early-out 返回模式，而是使用 if 块包裹全部逻辑作为临时方案。
