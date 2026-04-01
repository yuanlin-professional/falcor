# WaveOps.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Slang/WaveOps.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Slang

## 功能概述

测试 GPU 波前（Wave）级别的并行操作，如 WaveActiveSum、WavePrefixSum、WaveReadLaneAt 等。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `laneCount` | `RWBuffer` | 着色器资源 |
| `testData` | `Buffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testWaveGetLaneCount` | [1, 1, 1] | 计算着色器入口点 |
| `testWaveMatch` | [256, 1, 1] | 计算着色器入口点 |
| `testWaveMinMax` | [256, 1, 1] | 计算着色器入口点 |
| `testWaveMaxSimpleFloat` | [32, 1, 1] | 计算着色器入口点 |
| `testWaveMaxSimpleInt` | [32, 1, 1] | 计算着色器入口点 |


### 关联 C++ 测试

- [`WaveOps.cpp`](WaveOps.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
