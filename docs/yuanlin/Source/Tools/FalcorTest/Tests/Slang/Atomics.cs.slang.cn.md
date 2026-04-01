# Atomics.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/Atomics.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 GPU 着色器中的原子操作（InterlockedAdd、InterlockedMin 等），验证并发环境下的数据一致性。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `data` | `StructuredBuffer` | 着色器资源 |
| `resultBuf` | `RWByteAddressBuffer` | 着色器资源 |
| `resultTex` | `RWTexture2D` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testBufferAddF16` | [256, 1, 1] | 计算着色器入口点 |
| `testBufferAddF16_2` | [256, 1, 1] | 计算着色器入口点 |
| `testBufferAddF32` | [256, 1, 1] | 计算着色器入口点 |
| `testTextureAddF32` | [256, 1, 1] | 计算着色器入口点 |
| `copyResult` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.NVAPI`
- `on`


### 关联 C++ 测试

- [`Atomics.cpp`](Atomics.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
