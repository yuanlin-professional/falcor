# TemplatedLoad 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/TemplatedLoad.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 中的模板化资源加载操作，验证泛型资源访问。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `TemplatedScalarLoad16` | GPU 测试用例 |
| `TemplatedVectorLoad16` | GPU 测试用例 |
| `TemplatedMatrixLoad16_2x4` | GPU 测试用例 |
| `TemplatedMatrixLoad16_4x3` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/HostDeviceShared.slangh`
- `random`


### 关联着色器

- [`TemplatedLoad.cs.slang`](TemplatedLoad.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
