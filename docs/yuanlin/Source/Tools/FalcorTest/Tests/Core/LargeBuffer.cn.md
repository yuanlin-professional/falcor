# LargeBuffer 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/LargeBuffer.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试超大缓冲区（超过 4GB）的创建和 GPU 读写操作，验证大偏移量下的数据存取正确性。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `LargeBufferCopyRegion1` | GPU 测试用例 |
| `LargeBufferCopyRegion2` | GPU 测试用例 |
| `LargeBufferReadRawRoot1` | GPU 测试用例 |
| `LargeBufferReadStructuredRoot1` | GPU 测试用例 |
| `LargeBufferReadRawSRV1` | GPU 测试用例 |
| `LargeBufferReadStructuredSRV1` | GPU 测试用例 |
| `LargeBufferReadStructuredSRV2` | GPU 测试用例 |
| `LargeBufferReadStructuredSRV3` | GPU 测试用例 |
| `LargeBufferReadStructuredUintSRV1` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `random`


### 关联着色器

- [`LargeBuffer.cs.slang`](LargeBuffer.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
