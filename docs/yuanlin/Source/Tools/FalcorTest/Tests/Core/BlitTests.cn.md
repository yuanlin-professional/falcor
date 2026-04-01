# BlitTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/BlitTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试纹理拷贝操作的正确性，包括浮点纹理和无符号整数纹理在有过滤和无过滤模式下的拷贝。验证 2x 降采样时的均值滤波结果是否正确。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `BlitFloatNoFilter` | GPU 测试用例 |
| `BlitFloatFilter` | GPU 测试用例 |
| `BlitUintNoFilter` | GPU 测试用例 |
| `BlitUintFilter` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `random`


### 关联着色器

- [`BlitTests.cs.slang`](BlitTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
