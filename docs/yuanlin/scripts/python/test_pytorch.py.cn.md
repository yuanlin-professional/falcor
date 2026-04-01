# test_pytorch.py 脚本文档

> 路径: `scripts/python/test_pytorch.py`
> 类型: Python 脚本
> 模块: scripts/python

## 功能概述
该脚本是 Falcor-PyTorch 张量互操作性的测试脚本。它验证了两个方向的数据传输：将 PyTorch CUDA 张量传递给 Falcor 渲染通道，以及从 Falcor 渲染通道获取 PyTorch CUDA 张量。通过随机维度和偏移量进行多轮测试，确保数据传输的正确性。

## 主要功能/类

### `create_tensor(dim, offset, device)`
| 参数 | 类型 | 说明 |
|------|------|------|
| dim | `list[int]` | 三维张量尺寸 |
| offset | `int` | 值偏移量 |
| device | `torch.device` | 目标设备 |

**返回值**: `torch.Tensor` - 按线性索引 + 偏移量填充的 3D 张量

### `test_tensor_to_falcor(device, test_pass, iterations=10)`
| 参数 | 类型 | 说明 |
|------|------|------|
| device | `torch.device` | CUDA 设备 |
| test_pass | `falcor.RenderPass` | 测试渲染通道 |
| iterations | `int` | 测试迭代次数，默认 10 |

**返回值**: 无（测试失败时抛出 RuntimeError）

### `test_tensor_from_falcor(test_pass, iterations=10)`
| 参数 | 类型 | 说明 |
|------|------|------|
| test_pass | `falcor.RenderPass` | 测试渲染通道 |
| iterations | `int` | 测试迭代次数，默认 10 |

**返回值**: 无（测试失败时抛出 RuntimeError）

### `main()`
主测试函数，创建测试环境并运行 100 轮双向测试。

## 使用示例
```bash
python test_pytorch.py
# 输出 "SUCCESS!" 表示所有测试通过
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架
- `random` - 随机数生成（固定种子 1）

## 实现细节
- 使用 `TestPyTorchPass` 渲染通道进行测试
- 每次测试使用随机的 3D 尺寸（1-31 的随机值）
- **To Falcor 测试**：创建张量 → 传给 Falcor → Falcor 内部验证数据正确性
- **From Falcor 测试**：Falcor 生成数据 → 返回张量 → Python 验证类型、形状、设备和每个元素值
- 验证返回的张量必须为 CUDA float32 类型
- 使用固定随机种子确保测试可复现
- 数据填充规则：`value = linear_index + offset`
