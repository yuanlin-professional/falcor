# IntersectionHelpersTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/IntersectionHelpersTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试光线-几何体相交检测辅助函数，如光线-球体、光线-三角形相交。

## 类与接口

### `RayOriginLocation`

- **职责**: RayOriginLocation 类的核心功能实现


## 枚举类型

- `RayOriginLocation`


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `random`


### 关联着色器

- [`IntersectionHelpersTests.cs.slang`](IntersectionHelpersTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
