# SceneGradientsTest 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/DiffRendering/SceneGradientsTest.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/DiffRendering

## 功能概述

测试可微渲染中的场景级梯度计算，验证几何变换和材质参数的梯度回传。

## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `DiffRendering/SceneGradients.h`


### 关联着色器

- [`SceneGradientsTest.cs.slang`](SceneGradientsTest.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
