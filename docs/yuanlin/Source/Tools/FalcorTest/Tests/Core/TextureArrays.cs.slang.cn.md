# TextureArrays.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/TextureArrays.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试纹理数组的创建和 GPU 端采样，验证不同数组切片的数据读取正确性。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `tex` | `RWTexture2D` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testWrite` | [16, 16, 1] | 计算着色器入口点 |
| `testRead` | [16, 16, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`TextureArrays.cpp`](TextureArrays.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
