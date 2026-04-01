# diff_render_module.py 脚本文档（形状优化）

> 路径: `scripts/inv-rendering/shape_optimization/diff_render_module.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/shape_optimization

## 功能概述
该脚本实现了形状优化的可微分渲染模块。与材质优化版本不同，此模块的前向传播更新网格顶点数据（位置、法线、切线），反向传播计算这三类几何属性的梯度。

## 主要功能/类

### `class DiffRenderFunction(torch.autograd.Function)`
自定义 PyTorch 自动微分函数，封装形状相关的可微分渲染。

#### `forward(ctx, mesh_positions, mesh_normals, mesh_tangents, context)`
| 参数 | 类型 | 说明 |
|------|------|------|
| mesh_positions | `torch.Tensor` | 网格顶点位置，形状 (N, 3) |
| mesh_normals | `torch.Tensor` | 网格顶点法线，形状 (N, 3) |
| mesh_tangents | `torch.Tensor` | 网格顶点切线，形状 (N, 3) |
| context | `dict` | 渲染上下文 |

**返回值**: `torch.Tensor` - 渲染图像（HxWx3）

#### `backward(ctx, grad_output)`
计算网格位置、法线、切线的梯度。

**返回值**: `tuple` - (位置梯度, 法线梯度, 切线梯度, None)

### `class DiffRenderModule(torch.nn.Module)`
封装 DiffRenderFunction 的 PyTorch 模块。

#### `__init__(self, testbed, passes, scene, mesh, params, buffers, spp)`
| 参数 | 类型 | 说明 |
|------|------|------|
| testbed | `falcor.Testbed` | 测试平台 |
| passes | `dict` | 渲染通道字典 |
| scene | `falcor.Scene` | 场景对象 |
| mesh | `mesh_utils.Mesh` | 网格对象 |
| params | `dict` | 优化参数（含 mesh_id） |
| buffers | `dict` | Falcor 缓冲区（含 dL_dI） |
| spp | `dict` | 采样数配置 |

## 使用示例
```python
diff_render = DiffRenderModule(testbed, passes, scene, mesh, params, buffers, spp)
img = diff_render(mesh_positions, mesh_normals, mesh_tangents)
loss = compute_loss(img, target)
loss.backward()  # 自动计算几何梯度
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架
- `sys`, `os` - 路径管理
- `common` - 公共渲染工具
- `mesh_utils` - 网格工具

## 实现细节
- 前向传播：更新网格顶点位置/法线/切线 → 同步到 Falcor → 执行前向渲染
- 反向传播：设置 dL/dI → 执行反向渲染 → 提取三种梯度类型
- 梯度类型：`MeshPosition`、`MeshNormal`、`MeshTangent`
- 梯度从一维展平格式 reshape 为 (-1, 3) 的顶点格式
