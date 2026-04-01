# SlangExtension.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/SlangExtension.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 语言的扩展语法特性，验证自定义语法糖的正确解析。

## 结构体与接口

### `Test1`

结构体定义。

### `A`

结构体定义。

### `Test2`

结构体定义。

### `A`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`SlangExtension.cpp`](SlangExtension.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
