# RGLCommon 源码文档
> 路径: `Source/Falcor/Scene/Material/RGLCommon.h` + `RGLCommon.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了 RGL BRDF 采样所需的 4D 可采样分布表。给定坐标 (x, y)，从 4D 表中切出 2D 切片并进行采样得到 (z, w) 坐标。PDF 在各维度上线性插值，CDF 存储线性插值 PDF 的积分。

## 类与接口

### `SamplableDistribution4D`
- **继承**: 无
- **职责**: 构建可采样的 4D 分布表（包含 PDF、边际 CDF 和条件 CDF）

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `SamplableDistribution4D(const float* pdf, uint4 size)` | 从 PDF 数据构建分布表 |
| `const float* getPDF()` | 获取归一化的 PDF 数据 |
| `const float* getMarginal()` | 获取边际分布 CDF |
| `const float* getConditional()` | 获取条件分布 CDF |

## 依赖关系
### 本文件引用
- `Utils/Math/Vector.h` - 向量数学

### 被以下文件引用
- `RGLMaterial.cpp` - 构建 VNDF 和亮度分布表

## 实现细节
- 对每个 2D 切片构建边际和条件 CDF（类似 Pharr 等人的方法）
- 全零切片被重置为均匀分布
- CDF 基于线性插值 PDF 计算（梯形积分），最后归一化
