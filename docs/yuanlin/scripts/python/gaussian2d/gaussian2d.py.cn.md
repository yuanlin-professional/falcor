# gaussian2d.py 脚本文档

> 路径: `scripts/python/gaussian2d/gaussian2d.py`
> 类型: Python 脚本
> 模块: scripts/python/gaussian2d

## 功能概述
该脚本实现了使用 2D 高斯函数拟合目标图像的优化过程。它结合了 Falcor 计算着色器（前向渲染 + DiffSlang 反向传播）和 PyTorch 优化器，通过迭代优化一组加性 2D 高斯核的参数（位置、缩放、颜色、旋转），使合成图像逼近目标图像（默认为蒙娜丽莎）。

## 主要功能/类

### `class Splat2D`
管理 2D 高斯渲染的 GPU 资源和着色器调用。

#### `__init__(self, device)`
| 参数 | 类型 | 说明 |
|------|------|------|
| device | `falcor.Device` | Falcor GPU 设备 |

创建缓冲区（blobs、grad_blobs、image、grad_image）和计算通道（forward_main、backward_main）。

#### `forward(self, blobs)`
| 参数 | 类型 | 说明 |
|------|------|------|
| blobs | `torch.Tensor` | 高斯参数，形状 [BLOB_COUNT, 8] |

**返回值**: `torch.Tensor` - 渲染图像，形状 [RESOLUTION, RESOLUTION, 3]

#### `backward(self, blobs, grad_intensities)`
| 参数 | 类型 | 说明 |
|------|------|------|
| blobs | `torch.Tensor` | 高斯参数 |
| grad_intensities | `torch.Tensor` | 图像梯度 |

**返回值**: `torch.Tensor` - 高斯参数梯度，形状 [BLOB_COUNT, 8]

### `class Splat2DFunction(torch.autograd.Function)`
自定义 PyTorch 自动微分函数，桥接 Splat2D 和 PyTorch 优化。

### `class Splat2DModule(torch.nn.Module)`
封装 Splat2DFunction 的 PyTorch 模块。

### `optimize()`
执行优化循环的主函数。

**常量配置**:
| 常量 | 值 | 说明 |
|------|-----|------|
| BLOB_COUNT | 4096 | 高斯核数量 |
| RESOLUTION | 1024 | 图像分辨率 |
| ITERATIONS | 4000 | 优化迭代次数 |

## 使用示例
```bash
python gaussian2d.py
# 打开窗口实时显示优化过程
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架
- `pathlib.Path` - 路径处理
- `torch` - PyTorch 深度学习框架
- `numpy` - 数值计算
- `PIL.Image` - 目标图像加载

## 实现细节
- 每个高斯核有 8 个参数：position(2) + scale(2) + color(3) + rotation(1)
- 缩放使用对数空间（`torch.exp(blob_scales)`），颜色使用 sigmoid 激活
- 使用 Slang 着色器（`splat2d.cs.slang`）实现 GPU 前向和反向传播
- 前向渲染通过加性混合实现多个高斯的叠加
- 使用 L1 损失（`torch.nn.functional.l1_loss`）
- Adam 优化器学习率为 0.01
- 每 5 次迭代更新窗口显示，应用 gamma 校正（pow 2.2）后显示
- 缓冲区使用 Shared 标志实现 Falcor 和 PyTorch 之间的 GPU 数据共享
- 目标图像为 `media/test_images/monalisa.jpg`
