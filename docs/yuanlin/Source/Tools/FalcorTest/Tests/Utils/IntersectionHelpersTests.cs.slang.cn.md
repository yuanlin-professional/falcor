# IntersectionHelpersTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/IntersectionHelpersTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试光线-几何体相交检测辅助函数，如光线-球体、光线-三角形相交。

## 常量缓冲区

- `TestCB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `sphereCenter` | `StructuredBuffer` | 着色器资源 |
| `sphereRadius` | `StructuredBuffer` | 着色器资源 |
| `rayOrigin` | `StructuredBuffer` | 着色器资源 |
| `rayDir` | `StructuredBuffer` | 着色器资源 |
| `isectResult` | `RWStructuredBuffer` | 着色器资源 |
| `isectLoc` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testRaySphereIntersection` | [1, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Geometry.IntersectionHelpers`


### 关联 C++ 测试

- [`IntersectionHelpersTests.cpp`](IntersectionHelpersTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
