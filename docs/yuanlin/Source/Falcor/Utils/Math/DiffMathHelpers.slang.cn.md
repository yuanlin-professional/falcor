# DiffMathHelpers.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/DiffMathHelpers.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供可微分渲染中使用的无穷小量（infinitesimal）提取函数。用于从可微分变量中提取梯度分量。

## 函数
| 函数签名 | 说明 |
|----------|------|
| `float infinitesimal(float x)` | 提取标量的无穷小量部分（x - detach(x)） |
| `vector<float, N> infinitesimal(vector<float, N> x)` | 提取向量的无穷小量部分 |

## 依赖关系
### import
- 无外部依赖

## 实现细节

- 所有函数标记为 `[Differentiable]`、`[PreferRecompute]`、`[ForceInline]`，适用于自动微分管线。
- `detach(x)` 将变量从计算图中分离，因此 `x - detach(x)` 保留了仅包含梯度信息的无穷小部分。
