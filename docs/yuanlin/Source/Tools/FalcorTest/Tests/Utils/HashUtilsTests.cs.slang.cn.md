# HashUtilsTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/HashUtilsTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试各种哈希函数实现，如 xxHash、CityHash 等。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testJenkinsHash_PerfectHash` | [16, 16, 1] | 计算着色器入口点 |
| `testJenkinsHash` | [16, 16, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.HashUtils`


### 关联 C++ 测试

- [`HashUtilsTests.cpp`](HashUtilsTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
