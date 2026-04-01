# QuaternionTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/QuaternionTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试四元数的构造、乘法、插值和旋转操作。

## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Quaternion_Constructor` | CPU 测试用例 |
| `Quaternion_Access` | CPU 测试用例 |
| `Quaternion_Operator` | CPU 测试用例 |
| `Quaternion_Multiply` | CPU 测试用例 |
| `Quaternion_FloatChecks` | CPU 测试用例 |
| `Quaternion_Functions` | CPU 测试用例 |
| `Quaternion_slerp` | CPU 测试用例 |
| `Quaternion_Euler` | CPU 测试用例 |
| `Quaternion_quatFromAngleAxis` | CPU 测试用例 |
| `Quaternion_quatFromRotationBetweenVectors` | CPU 测试用例 |
| `Quaternion_quatFromEulerAngles` | CPU 测试用例 |
| `Quaternion_quatFromMatrix` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Math/Quaternion.h`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
