# AftermathTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/AftermathTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 NVIDIA Aftermath 工具在 GPU TDR（超时检测与恢复）场景下的崩溃转储捕获功能。通过在 GPU 上执行无限循环来强制触发 TDR，验证 Aftermath 能否正确记录崩溃信息。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `AftermathCatchTDR` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`


### 关联着色器

- [`AftermathTests.cs.slang`](AftermathTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
