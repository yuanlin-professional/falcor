# loss.py 脚本文档

> 路径: `scripts/inv-rendering/loss.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering

## 功能概述
该脚本提供了逆渲染优化中使用的各种损失函数，包括 L1 损失、L2 损失以及基于图像金字塔的多尺度损失。图像金字塔损失通过在多个分辨率尺度上计算误差，能够捕捉不同频率的图像差异，提高优化的收敛性和稳定性。

## 主要功能/类

### `compute_render_loss_L2(img, target_img, weight=1.0)`
| 参数 | 类型 | 说明 |
|------|------|------|
| img | `torch.Tensor` | 当前渲染图像 |
| target_img | `torch.Tensor` | 目标参考图像 |
| weight | `float` | 损失权重，默认 1.0 |

**返回值**: `torch.Tensor` - L2 损失值（0.5 * ||img - target||^2 * weight）

### `compute_render_loss_L1(img, target_img, weight=1.0)`
| 参数 | 类型 | 说明 |
|------|------|------|
| img | `torch.Tensor` | 当前渲染图像 |
| target_img | `torch.Tensor` | 目标参考图像 |
| weight | `float` | 损失权重，默认 1.0 |

**返回值**: `torch.Tensor` - L1 损失值（||img - target||_1 * weight）

### `downsample(input)`
| 参数 | 类型 | 说明 |
|------|------|------|
| input | `torch.Tensor` | 输入图像张量 |

**返回值**: `torch.Tensor` - 下采样 2x 后的图像

### `build_pyramid(img)`
| 参数 | 类型 | 说明 |
|------|------|------|
| img | `torch.Tensor` | 输入图像张量 |

**返回值**: `list[torch.Tensor]` - 图像金字塔列表（最多 5 层）

### `compute_render_loss_pyramid_L1(img, target_pyramid, weight=1.0)`
| 参数 | 类型 | 说明 |
|------|------|------|
| img | `torch.Tensor` | 当前渲染图像 |
| target_pyramid | `list[torch.Tensor]` | 目标图像金字塔 |
| weight | `float` | 损失权重 |

**返回值**: `torch.Tensor` - 金字塔多尺度 L1 损失

### `compute_render_loss_pyramid_L2(img, target_pyramid, weight=1.0)`
| 参数 | 类型 | 说明 |
|------|------|------|
| img | `torch.Tensor` | 当前渲染图像 |
| target_pyramid | `list[torch.Tensor]` | 目标图像金字塔 |
| weight | `float` | 损失权重 |

**返回值**: `torch.Tensor` - 金字塔多尺度 L2 损失

## 使用示例
```python
from loss import compute_render_loss_L2, build_pyramid, compute_render_loss_pyramid_L1

# 简单 L2 损失
loss = compute_render_loss_L2(rendered_img, target_img)

# 金字塔多尺度损失
target_pyramid = build_pyramid(target_img)
loss = compute_render_loss_pyramid_L1(rendered_img, target_pyramid)
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 张量运算
- `math` - 数学运算（用于对数计算）

## 实现细节
- 所有损失函数都将 NaN 值替换为 0，防止渲染中的无效像素影响优化
- 下采样使用简单的 2x2 均值池化（box filter）
- 图像金字塔最多 5 层，每层分辨率减半
- 金字塔损失中每层权重为 `4^i`，即低分辨率层的权重更高，用于平衡不同尺度的贡献
- 处理奇数尺寸图像时通过复制最后一行/列来填充
