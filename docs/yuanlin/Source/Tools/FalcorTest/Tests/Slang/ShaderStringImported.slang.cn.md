# ShaderStringImported.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/ShaderStringImported.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

被 ShaderStringImport 测试导入使用的着色器字符串模块。

## 依赖关系 / import

- `GeneratedModule`


### 关联 C++ 测试

- [`ShaderStringImported.cpp`](ShaderStringImported.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
