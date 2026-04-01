# SceneGradientsTest.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/DiffRendering/SceneGradientsTest.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/DiffRendering

## 功能概述

测试可微渲染中的场景级梯度计算，验证几何变换和材质参数的梯度回传。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `grads` | `ByteAddressBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `atomicAdd` | [4, 16, 1] | 计算着色器入口点 |
| `testAggregateGradients` | [4, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `DiffRendering.SceneGradients`


### 关联 C++ 测试

- [`SceneGradientsTest.cpp`](SceneGradientsTest.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
