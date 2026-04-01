# MatrixTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/MatrixTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试矩阵类型的构造、运算和变换操作。

## 测试用例

### CPU 测试

| 测试名称 | 说明 |
|----------|------|
| `Matrix_Constructor` | CPU 测试用例 |
| `Matrix_multilply` | CPU 测试用例 |
| `Matrix_transformPoint` | CPU 测试用例 |
| `Matrix_transformVector` | CPU 测试用例 |
| `Matrix_transpose` | CPU 测试用例 |
| `Matrix_translate` | CPU 测试用例 |
| `Matrix_rotate` | CPU 测试用例 |
| `Matrix_scale` | CPU 测试用例 |
| `Matrix_determinant` | CPU 测试用例 |
| `Matrix_inverse` | CPU 测试用例 |
| `Matrix_extractEulerAngleXYZ` | CPU 测试用例 |
| `Matrix_decompose` | CPU 测试用例 |
| `Matrix_matrixFromCoefficients` | CPU 测试用例 |
| `Matrix_matrixFromColumns` | CPU 测试用例 |
| `Matrix_matrixFromDiagonal` | CPU 测试用例 |
| `Matrix_perspective` | CPU 测试用例 |
| `Matrix_ortho` | CPU 测试用例 |
| `Matrix_matrixFromTranslation` | CPU 测试用例 |
| `Matrix_matrixFromRotation` | CPU 测试用例 |
| `Matrix_matrixFromRotationXYZ` | CPU 测试用例 |
| `Matrix_matrixFromScaling` | CPU 测试用例 |
| `Matrix_matrixFromLookAt` | CPU 测试用例 |
| `Matrix_matrixFromQuat` | CPU 测试用例 |
| `Matrix_FloatFormatter` | CPU 测试用例 |
| `Matrix_Json` | CPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Math/Matrix.h`
- `Utils/Math/MatrixJson.h`
- `fmt/format.h`
- `iostream`


## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
