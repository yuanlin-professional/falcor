# common.py 脚本文档

> 路径: `scripts/inv-rendering/common.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering

## 功能概述
该脚本是逆渲染流程的公共工具模块，提供了创建 Falcor 测试平台、加载场景、创建渲染通道、执行前向渲染和反向梯度渲染等核心功能。所有逆渲染优化脚本都依赖此模块来与 Falcor 渲染引擎进行交互。

## 主要功能/类

### `load_scene(testbed, scene_path, aspect_ratio=1.0)`
| 参数 | 类型 | 说明 |
|------|------|------|
| testbed | `falcor.Testbed` | Falcor 测试平台实例 |
| scene_path | `Path` | 场景文件路径 |
| aspect_ratio | `float` | 相机宽高比，默认 1.0 |

**返回值**: `falcor.Scene` - 加载的场景对象

**功能细节**:
- 使用 `DontMergeMaterials`、`RTDontMergeDynamic`、`DontOptimizeMaterials` 标志加载场景
- 禁用分析光源和环境光
- 设置相机宽高比

### `create_testbed(reso)`
| 参数 | 类型 | 说明 |
|------|------|------|
| reso | `(int, int)` | 分辨率，(宽, 高) |

**返回值**: `falcor.Testbed` - 创建的测试平台实例

**功能细节**:
- 创建带窗口的测试平台（GPU 0）
- 隐藏 UI、暂停时钟

### `create_passes(testbed, max_bounces, use_war)`
| 参数 | 类型 | 说明 |
|------|------|------|
| testbed | `falcor.Testbed` | 测试平台实例 |
| max_bounces | `int` | 最大光线反弹次数 |
| use_war | `bool` | 是否使用 WAR（Warped-Area Reparameterization） |

**返回值**: `dict` - 包含 `primal_accumulate`、`grad_accumulate`、`war_diff_pt` 的通道字典

### `render_primal(spp, testbed, passes)`
| 参数 | 类型 | 说明 |
|------|------|------|
| spp | `int` | 每像素采样数 |
| testbed | `falcor.Testbed` | 测试平台实例 |
| passes | `dict` | 渲染通道字典 |

**返回值**: `torch.Tensor` - 渲染图像张量（HxWx3，CUDA）

### `render_grad(spp, testbed, passes, dL_dI_buffer)`
| 参数 | 类型 | 说明 |
|------|------|------|
| spp | `int` | 每像素采样数 |
| testbed | `falcor.Testbed` | 测试平台实例 |
| passes | `dict` | 渲染通道字典 |
| dL_dI_buffer | `falcor.Buffer` | 损失对图像的梯度缓冲区 |

**返回值**: `dict` - 按梯度类型分类的梯度字典

## 使用示例
```python
import common

# 创建测试平台
testbed = common.create_testbed([512, 512])

# 创建渲染通道
passes = common.create_passes(testbed, max_bounces=4, use_war=True)

# 加载场景
scene = common.load_scene(testbed, "scene.pyscene", 1.0)

# 前向渲染
img = common.render_primal(64, testbed, passes)

# 反向渲染（计算梯度）
grads = common.render_grad(64, testbed, passes, dL_dI_buffer)
```

## 依赖关系
### 导入的模块
- `os` - 文件路径操作
- `sys` - 系统路径管理
- `argparse` - 命令行参数解析
- `signal` - 信号处理
- `glob` - 文件模式匹配
- `pathlib.Path` - 路径操作
- `torch` - PyTorch 张量运算
- `falcor` - Falcor 渲染框架
- `numpy` - 数值计算
- `pyexr` - EXR 图像读写
- `json` - JSON 处理
- `PIL.Image` - 图像处理
- `io` - IO 操作

## 实现细节
- `render_primal` 设置 `run_backward=0` 执行前向渲染，通过多帧累积实现指定 SPP
- `render_grad` 设置 `run_backward=1` 执行反向传播，先清除梯度再累积，最后聚合并除以 SPP 取均值
- 场景加载时禁用材质合并和优化，确保可微分渲染的参数独立性
- 梯度结果通过 `scene_gradients` 系统从 Falcor 传递到 PyTorch
