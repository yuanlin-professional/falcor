# GeometryHelpersTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/GeometryHelpersTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试几何计算辅助函数，包括法线变换、切线空间构建和球面坐标转换。

## 结构体与接口

### `AABB2D`

结构体定义。


## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `pos` | `StructuredBuffer` | 着色器资源 |
| `normal` | `StructuredBuffer` | 着色器资源 |
| `aabb` | `StructuredBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `spheres` | `Buffer` | 着色器资源 |
| `sinTheta` | `RWStructuredBuffer` | 着色器资源 |
| `origin` | `Buffer` | 着色器资源 |
| `coneDir` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testComputeRayOrigin` | [256, 1, 1] | 计算着色器入口点 |
| `testBoundSphereAngle` | [256, 1, 1] | 计算着色器入口点 |
| `testBoundingConeAngleAverage` | [256, 1, 1] | 计算着色器入口点 |
| `testBoundingConeAngleCenter` | [256, 1, 1] | 计算着色器入口点 |
| `testComputeClippedTriangleArea2D` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Geometry.GeometryHelpers`


### 关联 C++ 测试

- [`GeometryHelpersTests.cpp`](GeometryHelpersTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
