# LargeBuffer.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/LargeBuffer.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试超大缓冲区（超过 4GB）的创建和 GPU 读写操作，验证大偏移量下的数据存取正确性。

## 结构体与接口

### `S`

结构体定义。


## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `buffer` | `ByteAddressBuffer` | 着色器资源 |
| `structuredBuffer` | `StructuredBuffer` | 着色器资源 |
| `structuredBufferUint` | `StructuredBuffer` | 着色器资源 |
| `buffer` | `ByteAddressBuffer` | 着色器资源 |
| `structuredBuffer` | `StructuredBuffer` | 着色器资源 |
| `structuredBufferUint` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testReadRaw` | [256, 1, 1] | 计算着色器入口点 |
| `testReadStructured` | [256, 1, 1] | 计算着色器入口点 |
| `testReadStructuredUint` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Attributes`


### 关联 C++ 测试

- [`LargeBuffer.cpp`](LargeBuffer.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
