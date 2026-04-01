# BufferTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/BufferTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 ByteAddressBuffer、TypedBuffer 和 StructuredBuffer 三种缓冲区类型的读写修改（RMW）操作。包括清除、更新和读取内核，以及缓冲区大小验证。

## 类与接口

### `Type`

- **职责**: Type 类的核心功能实现


## 枚举类型

- `Type`


## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `RawBuffer` | GPU 测试用例 |
| `TypedBuffer` | GPU 测试用例 |
| `StructuredBuffer` | GPU 测试用例 |
| `BufferUpdate` | GPU 测试用例 |
| `BufferWrite` | GPU 测试用例 |
| `BufferStrides` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Math/Common.h`


### 关联着色器

- [`BufferTests.cs.slang`](BufferTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
