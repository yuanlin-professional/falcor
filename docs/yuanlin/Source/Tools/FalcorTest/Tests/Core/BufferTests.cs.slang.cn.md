# BufferTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/BufferTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 ByteAddressBuffer、TypedBuffer 和 StructuredBuffer 三种缓冲区类型的读写修改（RMW）操作。包括清除、更新和读取内核，以及缓冲区大小验证。

## 结构体与接口

### `BufferSizeTest1_12B_T`

结构体定义。

### `BufferSizeTest2_2B_T`

结构体定义。

### `BufferSizeTest3_24B_T`

结构体定义。

### `BufferSizeTest4_24B_T`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `buffer` | `RWByteAddressBuffer` | 着色器资源 |
| `buffer` | `RWBuffer` | 着色器资源 |
| `buffer` | `RWStructuredBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `as` | `RWStructuredBuffer` | 着色器资源 |
| `bufferSizeTest1_12B_buffer` | `RWStructuredBuffer` | 着色器资源 |
| `bufferSizeTest2_2B_buffer` | `RWStructuredBuffer` | 着色器资源 |
| `bufferSizeTest3_24B_buffer` | `RWStructuredBuffer` | 着色器资源 |
| `bufferSizeTest4_24B_buffer` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `clearBuffer` | [256, 1, 1] | 计算着色器入口点 |
| `updateBuffer` | [256, 1, 1] | 计算着色器入口点 |
| `readBuffer` | [256, 1, 1] | 计算着色器入口点 |
| `writeSizeTest` | [1, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`BufferTests.cpp`](BufferTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
