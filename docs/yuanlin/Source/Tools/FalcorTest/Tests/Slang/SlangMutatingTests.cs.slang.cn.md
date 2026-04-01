# SlangMutatingTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/SlangMutatingTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 语言中 [mutating] 属性修饰的方法，验证结构体值的原地修改语义。

## 结构体与接口

### `Data`

结构体定义。

### `DecodedData`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `buffer` | `Buffer` | 着色器资源 |
| `buf` | `Buffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`SlangMutatingTests.cpp`](SlangMutatingTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
