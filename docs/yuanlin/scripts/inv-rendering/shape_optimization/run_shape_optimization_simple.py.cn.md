# run_shape_optimization_simple.py 脚本文档

> 路径: `scripts/inv-rendering/shape_optimization/run_shape_optimization_simple.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/shape_optimization

## 功能概述
该脚本是形状优化的简化版本，优化刚体变换参数（平移和旋转）而非直接优化顶点位置。与 `run_shape_optimization.py` 不同，此脚本通过优化 3 个平移分量和 1 个旋转角度（绕 Y 轴），使初始对象与目标对齐。适用于姿态估计和刚体对齐场景。

## 主要功能/类

### `main(args)`
| 参数 | 类型 | 说明 |
|------|------|------|
| args | `argparse.Namespace` | 命令行参数 |

**返回值**: 无

**工作流程**:
1. 创建 `ShapeExample` 实例（使用默认场景，最大 2 次反弹）
2. 初始化优化变量：平移向量（3D）和旋转角度（1D，绕 Y 轴）
3. 使用 Adam 优化器（平移 lr=3e-4，旋转 lr=3e-3）
4. 迭代优化：
   - 计算旋转矩阵并应用于顶点
   - 应用平移
   - 重新计算法线和切线
   - 可微分渲染 → L2 损失 → 反向传播 → 优化器更新
5. 定期导出渲染图像和损失曲线

## 使用示例
```bash
python run_shape_optimization_simple.py
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架
- `time` - 计时
- `numpy` - 数值计算
- `pyexr` - EXR 图像读写
- `sys`, `os` - 系统和路径操作
- `dataclasses` - 数据类
- `datetime` - 时间计算
- `glob` - 文件匹配
- `argparse` - 命令行参数
- `common` - 公共渲染工具
- `transform_utils.axis_angle_rotation` - 轴角旋转矩阵
- `loss.compute_render_loss_L2` - L2 损失函数
- `shape_example.ShapeExample` - 形状优化示例配置

## 实现细节
- 优化参数仅为 4 个标量（3 个平移 + 1 个旋转角度），远少于顶点级优化
- 旋转矩阵通过 `axis_angle_rotation("Y", angle)` 生成并转置后应用于所有顶点
- 变换顺序：先旋转后平移（mesh_positions = R * positions + translation）
- 使用标准 Adam 优化器即可（无需 Large Steps），因为参数空间是低维的
- 旋转学习率（3e-3）大于平移学习率（3e-4），因为旋转角度变化对图像的影响更显著
- 使用 `capturable=True` 兼容 CUDA 张量
