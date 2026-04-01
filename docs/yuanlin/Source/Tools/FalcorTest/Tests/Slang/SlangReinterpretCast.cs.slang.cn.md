# SlangReinterpretCast.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/SlangReinterpretCast.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 中的 reinterpret<T>() 类型重解释转换操作。

## 结构体与接口

### `between`

结构体定义。

### `types`

结构体定义。

### `Blob`

结构体定义。

### `A`

结构体定义。

### `B`

结构体定义。

### `C`

结构体定义。

### `D`

结构体定义。

### `E`

结构体定义。

### `F`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `data` | `StructuredBuffer` | 着色器资源 |
| `resultA` | `RWStructuredBuffer` | 着色器资源 |
| `resultB` | `RWStructuredBuffer` | 着色器资源 |
| `resultC` | `RWStructuredBuffer` | 着色器资源 |
| `resultD` | `RWStructuredBuffer` | 着色器资源 |
| `resultE` | `RWStructuredBuffer` | 着色器资源 |
| `resultF` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [32, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`SlangReinterpretCast.cpp`](SlangReinterpretCast.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
