# StructuredBufferMatrix.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/StructuredBufferMatrix.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试结构化缓冲区中矩阵类型的存储和访问，验证行主序/列主序布局。

## 结构体与接口

### `should`

结构体定义。

### `Test1`

结构体定义。

### `Test2`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `data1` | `StructuredBuffer` | 着色器资源 |
| `data2` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testStructuredBufferMatrixLoad1` | [1, 1, 1] | 计算着色器入口点 |
| `testStructuredBufferMatrixLoad2` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`StructuredBufferMatrix.cpp`](StructuredBufferMatrix.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
