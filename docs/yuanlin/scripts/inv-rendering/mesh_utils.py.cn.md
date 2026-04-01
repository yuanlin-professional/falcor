# mesh_utils.py 脚本文档

> 路径: `scripts/inv-rendering/mesh_utils.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering

## 功能概述
该脚本提供了网格数据的管理和操作工具，包括在 Falcor 和 PyTorch 之间传输网格数据、计算平滑顶点法线和切线空间。这些功能是形状优化逆渲染流程的核心组件，法线和切线的计算算法参考了 nvdiffrec 和 MikkTSpace 标准。

## 主要功能/类

### 辅助函数

#### `dot(x, y)`
计算两个张量的逐元素点积，沿最后一维求和。

#### `length(x)`
计算向量长度。

#### `length_safe(x, eps)`
安全计算向量长度，避免零向量导致的 NaN 梯度。

#### `normalize_safe(x, eps)`
安全归一化向量。

### `class Mesh`
网格数据管理类，支持 Falcor 与 PyTorch 之间的数据交互。

#### `__init__(self, tri_idx, v_pos, v_norm, v_tangent, v_texcrd)`
| 参数 | 类型 | 说明 |
|------|------|------|
| tri_idx | `torch.Tensor` | 三角形索引，形状 (M, 3) |
| v_pos | `torch.Tensor` | 顶点位置，形状 (N, 3) |
| v_norm | `torch.Tensor` | 顶点法线，形状 (N, 3) |
| v_tangent | `torch.Tensor` | 顶点切线，形状 (N, 3) |
| v_texcrd | `torch.Tensor` | 纹理坐标，形状 (N, 3) |

#### `init_falcor(self, device, vertex_count, triangle_count)`
初始化 Falcor 缓冲区，用于 GPU 数据传输。

#### `load_from_falcor(self, testbed, mesh_id)`
从 Falcor 场景加载网格数据到 PyTorch 张量。

#### `update_to_falcor(self, testbed, mesh_id)`
将 PyTorch 张量中的网格数据更新回 Falcor 场景。

#### `compute_shading_frame(self)`
计算着色帧（法线和切线）。

**返回值**: `tuple(torch.Tensor, torch.Tensor)` - (法线, 切线)

#### `compute_normals(self)`
计算平滑顶点法线，通过累积面法线到顶点实现。

**返回值**: `torch.Tensor` - 顶点法线，形状 (N, 3)

#### `compute_tangents(self)`
计算切线空间，遵循 MikkTSpace 标准。

**返回值**: `torch.Tensor` - 顶点切线，形状 (N, 3)

## 使用示例
```python
import mesh_utils

mesh = mesh_utils.Mesh()
mesh.load_from_falcor(testbed, mesh_id=6)

# 修改顶点位置后重新计算法线和切线
mesh.v_pos = new_positions
normals, tangents = mesh.compute_shading_frame()

# 更新到 Falcor
mesh.update_to_falcor(testbed, mesh_id=6)
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 张量运算
- `falcor` - Falcor 渲染框架
- `numpy` - 数值计算

## 实现细节
- 常量 `EPS = 1e-10` 用于避免除零和 NaN 梯度
- 法线计算：通过 `scatter_add_` 将面法线累积到顶点，然后归一化；退化法线默认替换为 (0, 0, 1)
- 切线计算：基于纹理坐标差分计算切线方向，确保与法线正交（Gram-Schmidt 正交化）
- 退化切线的默认方向根据法线方向选择，避免与法线平行
- Falcor 缓冲区使用 struct_size=12（float3），支持 ShaderResource、UnorderedAccess 和 Shared 绑定标志
- 数据传输通过 `copy_to_torch` 和 `from_torch` 方法实现 GPU 到 GPU 的零拷贝传输
