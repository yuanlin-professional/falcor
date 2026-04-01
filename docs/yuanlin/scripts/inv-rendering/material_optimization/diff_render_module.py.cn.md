# diff_render_module.py 脚本文档（材质优化）

> 路径: `scripts/inv-rendering/material_optimization/diff_render_module.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/material_optimization

## 功能概述
该脚本实现了材质优化的可微分渲染模块。它定义了自定义的 PyTorch 自动微分函数，在前向传播中使用 Falcor 渲染图像，在反向传播中通过 WAR 可微分路径追踪器计算材质参数的梯度。支持 Standard、PBRTDiffuse 和 PBRTConductor 三种材质类型的参数优化。

## 主要功能/类

### `class DiffRenderFunction(torch.autograd.Function)`
自定义 PyTorch 自动微分函数，封装可微分渲染的前向和反向传播。

#### `forward(ctx, standard_base_color, standard_metallic, standard_roughness, diffuse_color, conductor_eta, conductor_k, conductor_roughness, context)`
| 参数 | 类型 | 说明 |
|------|------|------|
| standard_base_color | `torch.Tensor` | Standard 材质基础颜色 |
| standard_metallic | `torch.Tensor` | Standard 材质金属度 |
| standard_roughness | `torch.Tensor` | Standard 材质粗糙度 |
| diffuse_color | `torch.Tensor` | PBRTDiffuse 漫反射颜色 |
| conductor_eta | `torch.Tensor` | PBRTConductor 折射率 |
| conductor_k | `torch.Tensor` | PBRTConductor 消光系数 |
| conductor_roughness | `torch.Tensor` | PBRTConductor 粗糙度 |
| context | `dict` | 渲染上下文 |

**返回值**: `torch.Tensor` - 渲染图像（HxWx3）

#### `backward(ctx, grad_output)`
执行反向渲染，计算各材质参数的梯度。

**返回值**: `tuple` - 7 个材质参数的梯度 + None

### `class DiffRenderModule(torch.nn.Module)`
封装 DiffRenderFunction 的 PyTorch 模块。

#### `__init__(self, testbed, passes, scene, params, buffers, spp)`
| 参数 | 类型 | 说明 |
|------|------|------|
| testbed | `falcor.Testbed` | 测试平台 |
| passes | `dict` | 渲染通道字典 |
| scene | `falcor.Scene` | 场景对象 |
| params | `dict` | 材质参数配置 |
| buffers | `dict` | Falcor 缓冲区 |
| spp | `dict` | 采样数配置（primal/grad） |

## 使用示例
```python
diff_render = DiffRenderModule(testbed, passes, scene, params, buffers, spp)
img = diff_render(base_color, metallic, roughness, diffuse, eta, k, conductor_roughness)
loss = compute_loss(img, target)
loss.backward()  # 自动计算材质参数梯度
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架
- `sys`, `os` - 路径管理
- `common` - 公共渲染工具（前向/反向渲染）
- `material_utils` - 材质参数转换工具

## 实现细节
- 前向传播：将分离的材质参数重新组合为字典 → 转换为 Falcor 原始格式 → 设置到场景 → 执行前向渲染
- 反向传播：设置 dL/dI 缓冲区 → 执行反向渲染 → 从原始梯度中提取各材质类型的梯度
- 梯度类型为 `falcor.GradientType.Material`
- 通过 `material_utils.raw_params_to_dicts` 将原始梯度分解为各材质参数的梯度
