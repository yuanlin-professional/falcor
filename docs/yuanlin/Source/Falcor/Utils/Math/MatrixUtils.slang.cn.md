# MatrixUtils.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/MatrixUtils.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供 GPU 端的矩阵工具函数和变换结构体。包括从列向量构建矩阵、仿射变换封装、点和向量的变换操作，以及方阵的单位矩阵扩展。

## 结构体与接口

### `xform4x4`
| 字段 | 类型 | 说明 |
|------|------|------|
| `xform` | `row_major float4x4` | 4x4 变换矩阵 |

### `xform3x4`
| 字段 | 类型 | 说明 |
|------|------|------|
| `xform` | `row_major float3x4` | 3x4 仿射变换矩阵 |

## 函数
| 函数签名 | 说明 |
|----------|------|
| `float3x3 float3x3FromCols(float3, float3, float3)` | 从三个列向量构建 3x3 矩阵 |
| `float3 extractTranslate(float4x4 m)` | 提取 4x4 矩阵的平移分量 |
| `xform3x4 concat(xform3x4, xform3x4)` | 连接两个仿射变换 |
| `float3 transformPoint(xform4x4, float3)` | 用 4x4 矩阵变换点（带透视除法） |
| `float3 transformPointAffine(xform4x4, float3)` | 用 4x4 矩阵变换点（仿射，无透视除法） |
| `float3 transformPoint(xform3x4, float3)` | 用 3x4 矩阵变换点 |
| `float3 transformVector(xform4x4/xform3x4, float3)` | 变换向量（w=0） |
| `matrix<T,N,N>.identity()` | 方阵单位矩阵扩展方法 |

## 依赖关系
### import
- 无外部依赖
