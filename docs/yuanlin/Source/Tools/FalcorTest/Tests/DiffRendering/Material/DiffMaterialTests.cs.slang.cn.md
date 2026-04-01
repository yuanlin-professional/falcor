# DiffMaterialTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/DiffRendering/Material/DiffMaterialTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/DiffRendering/Material

## 功能概述

测试可微渲染中的材质梯度计算，验证材质参数变化对渲染结果的梯度传播正确性。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `materialGrad` | `RWStructuredBuffer` | 着色器资源 |
| `geometryGrad` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testDiffPBRTDiffuse` | [1, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Scene.Shading`
- `Utils.Sampling.UniformSampleGenerator`
- `DiffRendering.SceneGradients`
- `DiffRendering.GradientIOWrapper`


### 关联 C++ 测试

- [`DiffMaterialTests.cpp`](DiffMaterialTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
