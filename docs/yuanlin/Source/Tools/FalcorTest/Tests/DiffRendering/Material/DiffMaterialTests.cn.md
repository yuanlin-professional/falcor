# DiffMaterialTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/DiffRendering/Material/DiffMaterialTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/DiffRendering/Material

## 功能概述

测试可微渲染中的材质梯度计算，验证材质参数变化对渲染结果的梯度传播正确性。

## 结构体

- `BsdfConfig`: 数据结构定义


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Scene/Scene.h`
- `Scene/Material/PBRT/PBRTDiffuseMaterial.h`
- `DiffRendering/SceneGradients.h`
- `iostream`


### 关联着色器

- [`DiffMaterialTests.cs.slang`](DiffMaterialTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
