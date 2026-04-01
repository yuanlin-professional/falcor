# VectorTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/VectorTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试向量类型（float2/3/4, int2/3/4 等）的构造、运算和比较。

## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Vector_FloatFormatter` | CPU 测试用例 |
| `Vector_IntFormatter` | CPU 测试用例 |
| `Vector_Comparison` | CPU 测试用例 |
| `Vector_Json` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Math/Vector.h`
- `Utils/Math/VectorJson.h`
- `fmt/format.h`
- `nlohmann/json.hpp`
- `iostream`
- `algorithm`
- `random`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
