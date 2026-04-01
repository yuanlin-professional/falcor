# RootBufferParamBlockTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/RootBufferParamBlockTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试通过参数块（ParameterBlock）绑定的根缓冲区访问，验证着色器中对根级别结构化缓冲区的读写。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `RootBufferParamBlockSRV_6_0` | GPU 测试用例 |
| `RootBufferParamBlockUAV_6_0` | GPU 测试用例 |
| `RootBufferParamBlockSRV_6_3` | GPU 测试用例 |
| `RootBufferParamBlockUAV_6_3` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Core/Pass/ComputePass.h`
- `random`


### 关联着色器

- [`RootBufferParamBlockTests.cs.slang`](RootBufferParamBlockTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
