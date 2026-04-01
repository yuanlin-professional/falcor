# ResourceAliasing.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/ResourceAliasing.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 GPU 资源别名（Resource Aliasing）功能，验证两个资源共享相同物理内存时的屏障和同步操作。

## 结构体与接口

### `S`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `bufA1` | `StructuredBuffer` | 着色器资源 |
| `bufA2` | `StructuredBuffer` | 着色器资源 |
| `bufA3` | `StructuredBuffer` | 着色器资源 |
| `bufB1` | `RWStructuredBuffer` | 着色器资源 |
| `bufB2` | `RWStructuredBuffer` | 着色器资源 |
| `bufB3` | `RWStructuredBuffer` | 着色器资源 |
| `bufStruct1` | `StructuredBuffer` | 着色器资源 |
| `bufStruct2` | `StructuredBuffer` | 着色器资源 |
| `bufStruct3` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testRead` | [32, 1, 1] | 计算着色器入口点 |
| `testReadWrite` | [32, 1, 1] | 计算着色器入口点 |
| `testStructRead` | [32, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`ResourceAliasing.cpp`](ResourceAliasing.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
