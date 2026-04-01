# AftermathTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/AftermathTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试 NVIDIA Aftermath 工具在 GPU TDR（超时检测与恢复）场景下的崩溃转储捕获功能。通过在 GPU 上执行无限循环来强制触发 TDR，验证 Aftermath 能否正确记录崩溃信息。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [32, 32, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`AftermathTests.cpp`](AftermathTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
