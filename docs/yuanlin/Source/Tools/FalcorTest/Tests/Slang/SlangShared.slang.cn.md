# SlangShared.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/SlangShared.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

定义被多个 Slang 测试共享引用的结构体和接口类型。

### 关联 C++ 测试

- [`SlangShared.cpp`](SlangShared.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
