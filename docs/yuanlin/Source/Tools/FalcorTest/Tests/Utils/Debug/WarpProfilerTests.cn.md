# WarpProfilerTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/Debug/WarpProfilerTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils/Debug

## 功能概述

测试 GPU Warp/Wave 级别的性能分析工具。

## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Debug/WarpProfiler.h`


### 关联着色器

- [`WarpProfilerTests.cs.slang`](WarpProfilerTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
