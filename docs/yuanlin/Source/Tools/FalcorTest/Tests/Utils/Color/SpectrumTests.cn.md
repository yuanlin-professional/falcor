# SpectrumTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/Color/SpectrumTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils/Color

## 功能概述

测试光谱数据处理功能，包括光谱曲线的加载和操作。

## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `SpectrumXYZ` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Color/Spectrum.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
