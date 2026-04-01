# InheritanceTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/InheritanceTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 语言的接口继承和多态调度机制，验证虚函数分派的正确性。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Inheritance_ManualCreate` | GPU 测试用例 |
| `Inheritance_ConformanceCreate` | GPU 测试用例 |
| `Inheritance_CheckInvalid` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/HostDeviceShared.slangh`
- `limits`
- `random`


### 关联着色器

- [`InheritanceTests.cs.slang`](InheritanceTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
