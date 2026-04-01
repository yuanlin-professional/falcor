# ParamBlockDefinition.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/ParamBlockDefinition.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

定义用于参数块测试的 Slang 结构体和常量缓冲区布局，被 ParamBlockCB 和 ParamBlockReflection 测试引用。

## 结构体与接口

### `TestBlock`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `bufA` | `ByteAddressBuffer` | 着色器资源 |
| `bufB` | `RWBuffer` | 着色器资源 |
| `testBuffer` | `RWByteAddressBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |
| `testBuffer` | `ByteAddressBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |
| `bufC` | `Buffer` | 着色器资源 |


## 依赖关系 / import

- `Utils.Attributes`


### 关联 C++ 测试

- [`ParamBlockDefinition.cpp`](ParamBlockDefinition.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
