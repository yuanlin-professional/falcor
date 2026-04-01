# AABBTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/AABBTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试 AABB（轴对齐包围盒）的构建、合并、相交检测和变换操作。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `testData` | `StructuredBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testAABB` | [1, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.AABB`


### 关联 C++ 测试

- [`AABBTests.cpp`](AABBTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
