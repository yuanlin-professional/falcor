# PointSetsTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Sampling/PointSetsTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Sampling

## 功能概述

测试各种采样点集生成器，验证 Hammersley、Halton 等点集的分布特性。

## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`


### 关联着色器

- [`PointSetsTests.cs.slang`](PointSetsTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
