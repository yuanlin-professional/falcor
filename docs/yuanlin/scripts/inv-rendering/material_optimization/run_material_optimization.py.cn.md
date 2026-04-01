# run_material_optimization.py 脚本文档

> 路径: `scripts/inv-rendering/material_optimization/run_material_optimization.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/material_optimization

## 功能概述
该脚本是材质优化的主程序入口。它创建球体材质优化示例，使用可微分渲染和 Adam 优化器迭代优化材质参数（基础颜色、金属度、粗糙度等），使渲染结果匹配参考图像。优化过程中定期导出中间渲染结果和材质参数。

## 主要功能/类

### `main(args)`
| 参数 | 类型 | 说明 |
|------|------|------|
| args | `argparse.Namespace` | 命令行参数 |

**返回值**: 无

**功能细节**:
1. 创建 `SphereMaterialsExample` 逆渲染示例
2. 为不同材质类型和参数配置不同的学习率
3. 使用 Adam 优化器进行迭代优化
4. 每次迭代：前向渲染 → 计算 L2 图像损失 → 反向传播 → 更新参数 → 裁剪参数
5. 定期导出渲染图像（EXR 格式）和材质参数（NPY 格式）
6. 最终保存图像损失和参数损失曲线

**学习率配置**:
| 材质类型 | 参数 | 学习率 |
|---------|------|--------|
| Standard | base_color | 1e-2 |
| Standard | roughness | 3e-3 |
| Standard | metallic | 3e-3 |
| PBRTDiffuse | diffuse | 1e-2 |
| PBRTConductor | eta | 1e-2 |
| PBRTConductor | k | 1e-2 |
| PBRTConductor | roughness | 1e-2 |

## 使用示例
```bash
python run_material_optimization.py
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架
- `time` - 计时
- `numpy` - 数值计算
- `pyexr` - EXR 图像读写
- `sys`, `os` - 系统和路径操作
- `dataclasses` - 数据类支持
- `datetime` - 时间计算
- `glob` - 文件匹配
- `argparse` - 命令行参数
- `common` - 公共渲染工具
- `material_utils` - 材质参数工具
- `loss` - 损失函数
- `sphere_materials_example.SphereMaterialsExample` - 球体材质优化示例

## 实现细节
- 使用 `capturable=True` 解决 PyTorch Adam 优化器在 CUDA 上的兼容问题
- 每次优化步后通过 `clamp_material_params` 裁剪参数到有效范围
- 导出策略：前 50 次迭代中每 10 次导出，之后每 50 次导出一次
- 高质量导出图像使用 1024 SPP 渲染
- 同时跟踪图像空间损失和参数空间损失，用于评估优化效果
