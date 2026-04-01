# Quaternion 源码文档

> 路径: `Source/Falcor/Utils/Math/Quaternion.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

统一的四元数头文件，聚合了四元数类型定义和四元数运算。作为其他模块引用四元数功能的入口。

## 类与接口

本文件为聚合头文件，仅包含以下两个头文件：

- `QuaternionTypes.h` - 四元数类型定义
- `QuaternionMath.h` - 四元数运算函数

## 依赖关系
### 本文件引用
- `QuaternionTypes.h`
- `QuaternionMath.h`

### 被以下文件引用
- `MatrixMath.h`、`FalcorMath.h` 及所有需要旋转表示的模块
