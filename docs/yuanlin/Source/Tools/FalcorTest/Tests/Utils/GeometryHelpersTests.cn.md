# GeometryHelpersTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/GeometryHelpersTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试几何计算辅助函数，包括法线变换、切线空间构建和球面坐标转换。

## 结构体

- `BBoxTestCase`: 数据结构定义
- `TestCase`: 数据结构定义
- `TestCase`: 数据结构定义
- `AABB2D`: 数据结构定义


## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `BoxSubtendedConeAngleCenter` | GPU 测试用例 |
| `BoxSubtendedConeAngleAverage` | GPU 测试用例 |
| `BoxSubtendedConeAngleAverageRandoms` | GPU 测试用例 |
| `SphereSubtendedAngle` | GPU 测试用例 |
| `ComputeClippedTriangleArea2D` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/HostDeviceShared.slangh`
- `Utils/Math/Common.h`
- `random`
- `cmath`


### 关联着色器

- [`GeometryHelpersTests.cs.slang`](GeometryHelpersTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
