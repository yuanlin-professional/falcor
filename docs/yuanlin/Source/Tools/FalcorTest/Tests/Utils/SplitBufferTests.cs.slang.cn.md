# SplitBufferTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/SplitBufferTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试分割缓冲区（SplitBuffer）数据结构的分配和访问。

## 结构体与接口

### `S32B`

结构体定义。

### `S4B`

结构体定义。

### `SplitByteBuffer`

结构体定义。

### `SplitStructBuffer`

结构体定义。

### `RangeDesc`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `data` | `ByteAddressBuffer` | 着色器资源 |
| `data` | `StructuredBuffer` | 着色器资源 |
| `gByteBufferUint16_t3` | `RWStructuredBuffer` | 着色器资源 |
| `gByteBufferUint32_t3` | `RWStructuredBuffer` | 着色器资源 |
| `gSplitByteBuffer` | `Buffer` | 着色器资源 |
| `gStructuredBuffer` | `RWStructuredBuffer` | 着色器资源 |
| `gSplitStructBuffer` | `Buffer` | 着色器资源 |
| `gRangeDescs` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testSplitByteBuffer48b` | [256, 1, 1] | 计算着色器入口点 |
| `testSplitByteBuffer96b` | [256, 1, 1] | 计算着色器入口点 |
| `testSplitStructuredBuffer` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Attributes`


### 关联 C++ 测试

- [`SplitBufferTests.cpp`](SplitBufferTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
