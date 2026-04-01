# RootBufferStructTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/RootBufferStructTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试根缓冲区中包含结构体的数据读写，验证复杂结构体布局在 GPU 端的正确映射。

## 结构体与接口

### `is`

结构体定义。

### `T`

结构体定义。

### `S`

结构体定义。


## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `buf` | `Buffer` | 着色器资源 |
| `rwBuf` | `RWBuffer` | 着色器资源 |
| `rootBuf` | `RWStructuredBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |
| `rootBuf` | `StructuredBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Attributes`


### 关联 C++ 测试

- [`RootBufferStructTests.cpp`](RootBufferStructTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
