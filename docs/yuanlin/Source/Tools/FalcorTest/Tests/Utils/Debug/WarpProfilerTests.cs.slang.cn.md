# WarpProfilerTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/Debug/WarpProfilerTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils/Debug

## 功能概述

测试 GPU Warp/Wave 级别的性能分析工具。

## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [16, 16, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Debug.WarpProfiler`


### 关联 C++ 测试

- [`WarpProfilerTests.cpp`](WarpProfilerTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
