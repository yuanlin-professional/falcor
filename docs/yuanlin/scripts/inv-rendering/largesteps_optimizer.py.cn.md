# largesteps_optimizer.py 脚本文档

> 路径: `scripts/inv-rendering/largesteps_optimizer.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering

## 功能概述
该脚本实现了 Large Steps 优化器，用于网格形状优化中的顶点位置更新。Large Steps 方法通过在微分坐标空间中进行优化，避免了直接优化顶点位置时容易出现的网格退化问题（如三角形翻转、面积退化等），能够实现更大步长的稳定优化。

## 主要功能/类

### `class LargeSteps(torch.optim.Optimizer)`
Large Steps 优化器，继承自 PyTorch 的 Optimizer 基类。

#### `__init__(self, V, F, lr, betas, lambda_value)`
| 参数 | 类型 | 说明 |
|------|------|------|
| V | `list[torch.Tensor]` | 待优化的顶点位置列表，包含形状为 (N, 3) 的张量 |
| F | `torch.Tensor` | 三角形索引，形状为 (M, 3)，int32 |
| lr | `float` | 学习率，默认 0.1 |
| betas | `tuple` | Adam 动量参数，默认 (0.9, 0.999) |
| lambda_value | `float` | 拉普拉斯正则化权重，默认 0.1 |

#### `step(self)`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: 无

**功能细节**:
1. 从微分坐标 `u` 重建顶点位置 `V_next`
2. 将顶点梯度反向传播到微分坐标 `u`
3. 使用 AdamUniform 更新微分坐标
4. 从更新后的微分坐标重建新的顶点位置

#### `zero_grad(self)`
清除所有梯度，包括父类梯度和内部 AdamUniform 优化器的梯度。

## 使用示例
```python
from largesteps_optimizer import LargeSteps

# 创建优化器
mesh_positions = mesh.v_pos.clone().requires_grad_(True)
optimizer = LargeSteps([mesh_positions], mesh.tri_idx, lr=0.01, lambda_value=60.0)

# 优化循环
for i in range(num_iters):
    optimizer.zero_grad()
    loss = compute_loss(mesh_positions)
    loss.backward()
    optimizer.step()
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `largesteps.geometry.compute_matrix` - 计算拉普拉斯矩阵
- `largesteps.optimize.AdamUniform` - 均匀化 Adam 优化器
- `largesteps.parameterize.from_differential` - 从微分坐标重建顶点
- `largesteps.parameterize.to_differential` - 将顶点转换为微分坐标

## 实现细节
- 核心思想是在微分坐标空间（而非顶点位置空间）中进行优化
- 使用 Cholesky 分解求解线性系统，将微分坐标转换回顶点位置
- `lambda_value` 控制正则化强度，较大值导致更平滑的优化
- 内部使用 `AdamUniform` 优化器对微分坐标进行更新
- 参考论文：Large Steps in Inverse Rendering of Geometry (Baptiste Nicolet et al.)
