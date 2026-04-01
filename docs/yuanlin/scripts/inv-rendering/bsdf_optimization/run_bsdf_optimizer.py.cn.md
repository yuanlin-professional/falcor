# run_bsdf_optimizer.py 脚本文档

> 路径: `scripts/inv-rendering/bsdf_optimization/run_bsdf_optimizer.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/bsdf_optimization

## 功能概述
该脚本实现了 BSDF 参数优化器，通过可微分渲染在 BSDF 切片空间中优化材质参数。它使用 Falcor 内置的 BSDFOptimizer 通道计算 BSDF 梯度，结合 PyTorch 的 Adam 优化器迭代更新初始材质参数，使其逼近参考材质。

## 主要功能/类

### `class BSDFFunction(torch.autograd.Function)`
自定义 PyTorch 自动微分函数，封装 BSDF 评估和梯度计算。

#### `forward(ctx, x, context)`
| 参数 | 类型 | 说明 |
|------|------|------|
| ctx | `Context` | PyTorch 自动微分上下文 |
| x | `torch.Tensor` | 材质参数张量 |
| context | `dict` | 包含 device、scene、buffer 等的上下文字典 |

**返回值**: `torch.Tensor` - 参数空间的 L1 损失

#### `backward(ctx, grad_output)`
通过 `bsdf_optimizer.compute_bsdf_grads()` 计算 BSDF 梯度。

**返回值**: `tuple` - (参数梯度, None)

### `class BSDFEvalModule(torch.nn.Module)`
封装 BSDFFunction 的 PyTorch 模块。

#### `__init__(self, device, scene, bsdf_optimizer, material_ids_buffer, material_params_buffer, params_ref)`
| 参数 | 类型 | 说明 |
|------|------|------|
| device | `falcor.Device` | Falcor 设备 |
| scene | `falcor.Scene` | 场景对象 |
| bsdf_optimizer | `falcor.BSDFOptimizer` | BSDF 优化器通道 |
| material_ids_buffer | `falcor.Buffer` | 材质 ID 缓冲区 |
| material_params_buffer | `falcor.Buffer` | 材质参数缓冲区 |
| params_ref | `torch.Tensor` | 参考材质参数 |

### `main()`
主函数，执行完整的 BSDF 优化流程。

## 使用示例
```bash
python run_bsdf_optimizer.py
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架

## 实现细节
- 加载 `bsdf_optimizer.pyscene` 场景，包含初始材质（ID 0）和参考材质（ID 1）
- BSDF 切片分辨率设置为 256
- 使用 Adam 优化器，学习率 1e-2，eps 1e-6
- 迭代 200 次，每 10 次打印损失值
- 前向传播：设置材质参数 → 计算 L1 损失
- 反向传播：通过 Falcor 的 BSDFOptimizer 计算 BSDF 空间梯度
- 使用 `capturable=True` 解决 PyTorch 中 CUDA 张量步数的兼容性问题
- 材质参数通过共享缓冲区（Shared bind flag）在 Falcor 和 PyTorch 之间传递
