# TextureTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/TextureTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试纹理创建、格式转换和 GPU 读取操作，验证各种纹理格式在着色器中的采样结果。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `tex3D_uav` | `RWTexture3D` | 着色器资源 |
| `tex3D_srv` | `Texture3D` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testTexture3DWrite` | [16, 16, 1] | 计算着色器入口点 |
| `testTexture3DRead` | [16, 16, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`TextureTests.cpp`](TextureTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
