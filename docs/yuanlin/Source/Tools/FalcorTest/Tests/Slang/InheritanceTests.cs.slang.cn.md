# InheritanceTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/InheritanceTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 语言的接口继承和多态调度机制，验证虚函数分派的正确性。

## 结构体与接口

### `TestInterfaceBase`

结构体定义。

### `TestV0SubNeg`

结构体定义。

### `TestV1DefDef`

结构体定义。

### `TestV2DefNeg`

结构体定义。

### `TestV3SumDef`

结构体定义。

### `TestDefSumNeg`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `testType` | `StructuredBuffer` | 着色器资源 |
| `testValue` | `StructuredBuffer` | 着色器资源 |
| `data` | `StructuredBuffer` | 着色器资源 |
| `resultsInt` | `RWStructuredBuffer` | 着色器资源 |
| `resultsFloat` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testInheritanceManual` | [16, 1, 1] | 计算着色器入口点 |
| `testInheritanceConformance` | [16, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`InheritanceTests.cpp`](InheritanceTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
