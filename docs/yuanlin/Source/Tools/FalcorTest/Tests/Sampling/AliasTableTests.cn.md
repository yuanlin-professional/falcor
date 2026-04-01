# AliasTableTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Sampling/AliasTableTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Sampling

## 功能概述

测试别名表（Alias Table）离散分布采样算法，验证按指定权重的离散采样分布正确性。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `AliasTable` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Sampling/AliasTable.h`
- `hypothesis/hypothesis.h`
- `iostream`


### 关联着色器

- [`AliasTableTests.cs.slang`](AliasTableTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
