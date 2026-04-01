# Matrix 源码文档

> 路径: `Source/Falcor/Utils/Math/Matrix.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

统一的矩阵头文件，聚合了矩阵类型定义和矩阵运算。作为其他模块引用矩阵功能的入口。

## 类与接口

本文件为聚合头文件，仅包含以下两个头文件：

- `MatrixTypes.h` - 矩阵类型定义
- `MatrixMath.h` - 矩阵运算函数

## 依赖关系
### 本文件引用
- `MatrixTypes.h`
- `MatrixMath.h`

### 被以下文件引用
- `AABB.h`、`FalcorMath.h`、`MathHelpers.h` 及整个框架中需要矩阵操作的模块
