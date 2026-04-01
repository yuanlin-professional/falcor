# BlitTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/BlitTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试纹理拷贝操作的正确性，包括浮点纹理和无符号整数纹理在有过滤和无过滤模式下的拷贝。验证 2x 降采样时的均值滤波结果是否正确。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `tex` | `Texture2D` | 着色器资源 |
| `tex` | `Texture2D` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `readback` | [16, 16, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`BlitTests.cpp`](BlitTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
