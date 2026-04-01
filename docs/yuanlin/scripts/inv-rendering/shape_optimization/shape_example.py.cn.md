# shape_example.py 脚本文档

> 路径: `scripts/inv-rendering/shape_optimization/shape_example.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/shape_optimization

## 功能概述
该脚本定义了形状优化的示例配置类 `ShapeExample`。它负责初始化整个形状优化流程：创建测试平台、加载参考和初始场景、渲染参考图像、加载网格数据、配置场景梯度（位置/法线/切线），并构建可微分渲染模块。

## 主要功能/类

### `@dataclasses.dataclass class ShapeExample`
形状优化示例的数据类。

**配置属性**:
| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| width | `int` | 512 | 图像宽度 |
| height | `int` | 512 | 图像高度 |
| mesh_id | `int` | 6 | 待优化网格 ID |
| max_bounces | `int` | 0 | 最大间接反弹次数 |
| niter | `int` | 400 | 优化迭代次数 |
| lr | `float` | 0.01 | 学习率 |
| lambda_value | `float` | 60.0 | Large Steps 正则化参数 |
| spp_ref | `int` | 256 | 参考图像采样数 |
| spp_primal | `int` | 32 | 前向渲染采样数 |
| spp_grad | `int` | 32 | 梯度渲染采样数 |
| output_dir | `str` | `./results/` | 输出目录 |
| ref_scene_filepath | `str` | `bunny_ref.pyscene` | 参考场景（兔子） |
| init_scene_filepath | `str` | `bunny_init.pyscene` | 初始场景 |

#### `__post_init__(self)`
初始化步骤：
1. 创建测试平台和 WAR 渲染通道
2. 加载参考场景，渲染并导出参考图像
3. 加载初始场景，从 Falcor 加载网格数据
4. 设置待优化网格（`set_mesh_to_optimize`）
5. 配置场景梯度：MeshPosition、MeshNormal、MeshTangent（各维度 = vertex_count * 3，hash_size=128）
6. 创建 dL/dI 缓冲区
7. 渲染并导出初始图像
8. 构建 `DiffRenderModule`

**生成的属性**:
- `testbed` - 测试平台
- `passes` - 渲染通道
- `ref_img` - 参考图像
- `mesh` - 网格对象（mesh_utils.Mesh）
- `params` - 优化参数字典（含 mesh_id 和 mesh_positions）
- `diff_render` - 可微分渲染模块

## 使用示例
```python
from shape_example import ShapeExample

inv_ex = ShapeExample(max_bounces=2, niter=1000)
# inv_ex.mesh - 网格数据
# inv_ex.ref_img - 参考图像
# inv_ex.diff_render - 可微分渲染模块
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 深度学习框架
- `falcor` - Falcor 渲染框架
- `time` - 计时
- `numpy` - 数值计算
- `pyexr` - EXR 图像读写
- `sys`, `os` - 系统和路径操作
- `dataclasses` - Python 数据类
- `common` - 公共渲染工具
- `mesh_utils` - 网格工具
- `diff_render_module.DiffRenderModule` - 可微分渲染模块

## 实现细节
- 使用 WAR（use_war=True）进行形状优化，支持几何重参数化
- 三种梯度类型各自独立配置，使用 hash_size=128 的梯度哈希表
- 网格位置被克隆并分离（`detach().clone()`），作为优化的初始值
- 通过 `set_mesh_to_optimize` 告知渲染器哪个网格参与可微分渲染
- dL/dI 缓冲区大小为 width * height，每元素 12 字节（float3）
