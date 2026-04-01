# ShaderStringDynamic.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/ShaderStringDynamic.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

动态类型的着色器字符串测试计算着色器。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [32, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Tests.Slang.ShaderStringDynamicType`


### 关联 C++ 测试

- [`ShaderStringDynamic.cpp`](ShaderStringDynamic.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
