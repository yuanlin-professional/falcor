# Int64Tests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/Int64Tests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 64 位整数在 GPU 着色器中的运算支持，包括加减乘除和位操作。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `data` | `RWStructuredBuffer` | 着色器资源 |
| `data` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testInt64` | [16, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`Int64Tests.cpp`](Int64Tests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
