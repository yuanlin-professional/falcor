# MatrixTypes 源码文档

> 路径: `Source/Falcor/Utils/Math/MatrixTypes.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

定义 Falcor 的矩阵类型模板 `matrix<T, RowCount, ColCount>`。采用行优先存储，语义与 Slang 对齐。

## 类与接口
### `matrix<T, RowCount, ColCount>`
- **继承**: 无
- **职责**: 通用矩阵类型，支持 1-4 行/列，当前仅支持 float 标量类型

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `matrix()` | 默认构造为单位矩阵 |
| `static matrix zeros()` | 零矩阵 |
| `static matrix identity()` | 单位矩阵 |
| `RowType& operator[](int r)` | 行访问 |
| `RowType& getRow(int r)` / `void setRow(int r, ...)` | 行读写 |
| `ColType getCol(int col)` / `void setCol(int col, ...)` | 列读写 |
| `T* data()` | 原始数据指针 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mRows` | `RowType[RowCount]` | 行优先存储 |

### 类型别名
| 别名 | 说明 |
|------|------|
| `float2x2` | 2x2 浮点矩阵 |
| `float3x3` | 3x3 浮点矩阵 |
| `float3x4` | 3x4 浮点矩阵 |
| `float4x4` | 4x4 浮点矩阵 |

## 依赖关系
### 本文件引用
- `ScalarTypes.h`
- `VectorTypes.h`
- `Core/Error.h`
