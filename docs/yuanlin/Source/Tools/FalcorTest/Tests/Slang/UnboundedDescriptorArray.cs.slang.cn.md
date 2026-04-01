# UnboundedDescriptorArray.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/UnboundedDescriptorArray.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 Slang 中无界描述符数组（unbounded descriptor array）的绑定和访问。

## 结构体与接口

### `Resources`

结构体定义。


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `textures` | `Texture2D` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [4, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`UnboundedDescriptorArray.cpp`](UnboundedDescriptorArray.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
