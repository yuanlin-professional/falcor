# SplitBufferTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/SplitBufferTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试分割缓冲区（SplitBuffer）数据结构的分配和访问。

## 结构体

- `S32B`: 数据结构定义
- `S4B`: 数据结构定义
- `BaseTypeTrait`: 数据结构定义
- `RangeDesc`: 数据结构定义


## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `SplitBuffer_ByteBuffer_Large48b` | GPU 测试用例 |
| `SplitBuffer_ByteBuffer_Large96b` | GPU 测试用例 |
| `SplitBuffer_StructuredBuffer_Large4B` | GPU 测试用例 |
| `SplitBuffer_StructuredBuffer_Large32B` | GPU 测试用例 |
| `SplitBuffer_ByteBuffer_Many48b` | GPU 测试用例 |
| `SplitBuffer_ByteBuffer_Many96b` | GPU 测试用例 |
| `SplitBuffer_StructuredBuffer_Many4B` | GPU 测试用例 |
| `SplitBuffer_StructuredBuffer_Many32B` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/SplitBuffer.h`
- `random`
- `chrono`


### 关联着色器

- [`SplitBufferTests.cs.slang`](SplitBufferTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
