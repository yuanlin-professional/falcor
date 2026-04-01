# TemplatedLoad.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/TemplatedLoad.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 中的模板化资源加载操作，验证泛型资源访问。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `data` | `ByteAddressBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testTemplatedScalarLoad16` | [1, 1, 1] | 计算着色器入口点 |
| `testTemplatedVectorLoad16` | [1, 1, 1] | 计算着色器入口点 |
| `testTemplatedMatrixLoad16_2x4` | [1, 1, 1] | 计算着色器入口点 |
| `testTemplatedMatrixLoad16_4x3` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`TemplatedLoad.cpp`](TemplatedLoad.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
