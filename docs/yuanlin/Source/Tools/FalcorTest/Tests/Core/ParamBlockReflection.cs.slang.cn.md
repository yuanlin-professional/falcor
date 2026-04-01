# ParamBlockReflection.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/ParamBlockReflection.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

用于测试参数块反射信息的计算着色器，验证着色器反射 API 能否正确解析参数块结构。

## 依赖关系 / import

- `ParamBlockDefinition`


### 关联 C++ 测试

- [`ParamBlockReflection.cpp`](ParamBlockReflection.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
