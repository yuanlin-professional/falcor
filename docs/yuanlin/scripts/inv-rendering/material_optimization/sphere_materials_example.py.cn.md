# sphere_materials_example.py 脚本文档

> 路径: `scripts/inv-rendering/material_optimization/sphere_materials_example.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering/material_optimization

## 功能概述
该脚本定义了球体材质优化的示例配置类 `SphereMaterialsExample`。它负责初始化整个材质优化流程：创建 Falcor 测试平台、加载参考/初始场景、渲染参考图像、设置场景梯度系统、创建 GPU 缓冲区，并构建可微分渲染模块。该类是材质优化管线的核心配置与初始化入口。

## 主要功能/类

### `@dataclasses.dataclass class SphereMaterialsExample`
球体材质优化示例的数据类。

**配置属性**:
| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| width | `int` | 720 | 图像宽度 |
| height | `int` | 432 | 图像高度 |
| M | `int` | 7 | 球体阵列尺寸 |
| material_count | `int` | 343 (7^3) | 材质总数 |
| niter | `int` | 1000 | 优化迭代次数 |
| spp_ref | `int` | 2048 | 参考图像采样数 |
| spp_primal | `int` | 32 | 前向渲染采样数 |
| spp_grad | `int` | 32 | 梯度渲染采样数 |
| output_dir | `str` | `./results/` | 输出目录 |
| ref_scene_filepath | `str` | `spheres_material_ref.pyscene` | 参考场景路径 |
| init_scene_filepath | `str` | `spheres_material_init.pyscene` | 初始场景路径 |

#### `__post_init__(self)`
在数据类初始化后自动执行，完成以下步骤：
1. 创建 Falcor 测试平台和渲染通道
2. 加载参考场景，设置场景梯度（材质类型，维度 = material_count * PARAM_COUNT）
3. 创建 GPU 缓冲区（material_ids、material_params、dL_dI）
4. 获取参考材质参数和渲染参考图像
5. 加载初始场景，获取初始材质参数
6. 构建 `DiffRenderModule` 可微分渲染模块

**生成的属性**:
- `testbed` - Falcor 测试平台
- `passes` - 渲染通道字典
- `ref_img` - 参考图像（torch.Tensor）
- `ref_material_params` - 参考材质参数字典
- `init_material_params` - 初始材质参数字典
- `diff_render` - 可微分渲染模块

## 使用示例
```python
from sphere_materials_example import SphereMaterialsExample

# 创建实例（自动初始化所有组件）
inv_ex = SphereMaterialsExample()

# 使用可微分渲染模块
img = inv_ex.diff_render(base_color, metallic, roughness, diffuse, eta, k, conductor_roughness)

# 访问参考数据
ref_img = inv_ex.ref_img
ref_params = inv_ex.ref_material_params
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
- `material_utils` - 材质参数转换工具
- `diff_render_module.DiffRenderModule` - 可微分渲染模块

## 实现细节
- 场景梯度配置：hash_size=256，梯度维度 = 343 * PARAM_COUNT
- 禁用 WAR（use_war=False），因为材质优化不需要几何重参数化
- 最大反弹次数为 4，支持多次间接光照
- 参考图像和初始图像都以 EXR 格式导出到输出目录
- 缓冲区使用 ShaderResource | UnorderedAccess | Shared 标志，确保 Falcor 和 PyTorch 可以共享访问
