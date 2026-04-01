# RootBufferTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/RootBufferTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试根级别缓冲区的基本读写功能，验证从 CPU 传递的数据能否在 GPU 着色器中正确访问。

## 结构体与接口

### `S`

结构体定义。


## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `rawBuffer` | `ByteAddressBuffer` | 着色器资源 |
| `structBuffer` | `StructuredBuffer` | 着色器资源 |
| `testBuffer` | `RWByteAddressBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |
| `testBuffer` | `ByteAddressBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |
| `typedBufferUint` | `RWBuffer` | 着色器资源 |
| `typedBufferFloat4` | `Buffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Attributes`


### 关联 C++ 测试

- [`RootBufferTests.cpp`](RootBufferTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
