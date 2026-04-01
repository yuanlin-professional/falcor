# TextureLoadTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/TextureLoadTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

纹理加载计算着色器，用于在 GPU 端读取纹理数据并写入到结构化缓冲区中，配合 TextureTests.cpp 使用。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `texUnorm` | `Texture2D` | 着色器资源 |
| `texUnormAsUint` | `Texture2D` | 着色器资源 |
| `texUint` | `Texture2D` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testLoadFormat` | [256, 1, 1] | 计算着色器入口点 |
| `testLoadMips` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`TextureLoadTests.cpp`](TextureLoadTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
