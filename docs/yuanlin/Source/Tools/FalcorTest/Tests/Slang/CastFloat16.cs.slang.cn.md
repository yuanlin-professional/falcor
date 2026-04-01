# CastFloat16.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/CastFloat16.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 16 位浮点数（half/float16_t）与 32 位浮点数之间的转换，验证精度和边界情况。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `data` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testCastFloat16` | [16, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`CastFloat16.cpp`](CastFloat16.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
