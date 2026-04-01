# run_shape_optimization.py 脚本文档

> 路径: `scripts/inv-rendering/shape_optimization/run_shape_optimization.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/shape_optimization

## 功能概述
该脚本是形状优化（网格顶点位置优化）的主程序。它使用 Large Steps 优化器在微分坐标空间中优化网格顶点位置，通过可微分渲染使初始形状（球体）逐步变形为目标形状（兔子模型）。Large Steps 方法能够在保持网格质量的前提下实现较大的形变。

## 主要功能/类

### `main(args)`
| 参数 | 类型 | 说明 |
|------|------|------|
| args | `argparse.Namespace` | 命令行参数 |

**返回值**: 无

**工作流程**:
1. 创建 `ShapeExample` 实例（初始场景为 sphere_init.pyscene）
2. 创建 `LargeSteps` 优化器（lr=0.01，lambda=60.0）
3. 迭代 1000 次：
   - 更新顶点位置
   - 重新计算法线和切线
   - 可微分渲染
   - 计算 L2 图像损失
   - 反向传播
   - Large Steps 优化器更新
4. 定期导出渲染图像和损失曲线

## 使用示例
```bash
python run_shape_optimization.py
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
- `largesteps_optimizer.LargeSteps` - Large Steps 优化器
- `loss.compute_render_loss_L2` - L2 损失函数
- `shape_example.ShapeExample` - 形状优化示例配置

## 实现细节
- 使用 Large Steps 优化器而非标准 Adam，因为顶点位置的直接优化容易导致网格退化
- 每次迭代都需要重新计算法线和切线（`compute_shading_frame`），因为它们依赖于顶点位置
- 最大反弹次数设为 2，平衡渲染质量和计算效率
- 高质量导出图像使用 256 SPP
- 损失曲线保存为文本文件，便于后续分析
