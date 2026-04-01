# transform_utils.py 脚本文档

> 路径: `scripts/inv-rendering/transform_utils.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering

## 功能概述
该脚本提供了三维旋转变换的工具函数，支持将轴角旋转和欧拉角转换为旋转矩阵。代码源自 PyTorch3D 库，用于逆渲染中的形状变换优化（如物体的旋转和平移）。

## 主要功能/类

### `axis_angle_rotation(axis, angle)`
| 参数 | 类型 | 说明 |
|------|------|------|
| axis | `str` | 旋转轴标签，"X"、"Y" 或 "Z" |
| angle | `torch.Tensor` | 旋转角度（弧度），任意形状 |

**返回值**: `torch.Tensor` - 旋转矩阵，形状 (..., 3, 3)

### `euler_angles_to_matrix(euler_angles, convention)`
| 参数 | 类型 | 说明 |
|------|------|------|
| euler_angles | `torch.Tensor` | 欧拉角（弧度），形状 (..., 3) |
| convention | `str` | 旋转顺序约定，由三个大写字母组成（如 "XYZ"、"ZYX"） |

**返回值**: `torch.Tensor` - 旋转矩阵，形状 (..., 3, 3)

## 使用示例
```python
from transform_utils import axis_angle_rotation, euler_angles_to_matrix

# 绕 Y 轴旋转
angle = torch.tensor([0.5])  # 弧度
R = axis_angle_rotation("Y", angle)  # 形状 (1, 3, 3)

# 欧拉角到旋转矩阵
euler = torch.tensor([0.1, 0.2, 0.3])
R = euler_angles_to_matrix(euler, "XYZ")  # 形状 (3, 3)
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 张量运算

## 实现细节
- 代码来源于 PyTorch3D 库
- `axis_angle_rotation` 使用解析式构建旋转矩阵，支持批量操作
- `euler_angles_to_matrix` 将三个轴的旋转矩阵按指定顺序相乘
- 验证约定字符串：必须为 3 个字母，必须是 X/Y/Z，且相邻字母不能相同（如 "XXY" 无效）
- 所有操作支持自动微分，适合在优化循环中使用
