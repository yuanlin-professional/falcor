# PackedFormatsTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/PackedFormatsTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试数据打包/解包格式转换，如法线的八面体编码、颜色的 R11G11B10 打包等。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `testData` | `StructuredBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testLogLuvHDR` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.PackedFormats`


### 关联 C++ 测试

- [`PackedFormatsTests.cpp`](PackedFormatsTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
