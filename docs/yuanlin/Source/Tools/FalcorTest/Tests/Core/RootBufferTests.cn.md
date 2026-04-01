# RootBufferTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/RootBufferTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试根级别缓冲区的基本读写功能，验证从 CPU 传递的数据能否在 GPU 着色器中正确访问。

## 结构体

- `S`: 数据结构定义


## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `RootBufferSRV_6_0` | GPU 测试用例 |
| `RootBufferUAV_6_0` | GPU 测试用例 |
| `RootBufferSRV_6_3` | GPU 测试用例 |
| `RootBufferUAV_6_3` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `random`


### 关联着色器

- [`RootBufferTests.cs.slang`](RootBufferTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
