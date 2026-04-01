# ShaderModel 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/ShaderModel.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试不同着色器模型（SM 6.0-6.7）的特性支持，验证硬件能力查询。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `ShaderModel6_0` | GPU 测试用例 |
| `ShaderModel6_1` | GPU 测试用例 |
| `ShaderModel6_2` | GPU 测试用例 |
| `ShaderModel6_3` | GPU 测试用例 |
| `ShaderModel6_4` | GPU 测试用例 |
| `ShaderModel6_5` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`


### 关联着色器

- [`ShaderModel.cs.slang`](ShaderModel.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
