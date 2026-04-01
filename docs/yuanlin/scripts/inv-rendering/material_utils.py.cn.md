# material_utils.py 脚本文档

> 路径: `scripts/inv-rendering/material_utils.py`
> 类型: Python 脚本
> 模块: scripts/inv-rendering

## 功能概述
该脚本提供了材质参数的管理和转换工具函数，用于在 Falcor 原始材质参数格式和 Python 字典格式之间进行转换。支持三种材质类型：Standard（标准 PBR）、PBRTDiffuse（PBRT 漫反射）和 PBRTConductor（PBRT 导体）。同时提供了材质参数的损失计算、导出、裁剪等功能。

## 主要功能/类

### `raw_params_to_dicts(scene, material_ids, raw_params)`
| 参数 | 类型 | 说明 |
|------|------|------|
| scene | `falcor.Scene` | Falcor 场景对象 |
| material_ids | `torch.IntTensor` | 材质 ID 数组，形状 [M] |
| raw_params | `torch.FloatTensor` | 展平的原始参数，形状 [20*M] |

**返回值**: `dict` - 按材质类型组织的参数字典

### `dicts_to_raw_params(scene, material_ids, params_dicts, input_raw_params)`
| 参数 | 类型 | 说明 |
|------|------|------|
| scene | `falcor.Scene` | Falcor 场景对象 |
| material_ids | `torch.IntTensor` | 材质 ID 数组 |
| params_dicts | `dict` | 参数字典 |
| input_raw_params | `torch.FloatTensor` | 原始参数模板 |

**返回值**: `torch.FloatTensor` - 更新后的展平原始参数

### `compute_loss_params(params_dicts, ref_params_dicts)`
| 参数 | 类型 | 说明 |
|------|------|------|
| params_dicts | `dict` | 当前材质参数字典 |
| ref_params_dicts | `dict` | 参考材质参数字典 |

**返回值**: `float` - 参数空间的 L2 损失

### `output_material_params(filename, params_dicts)`
| 参数 | 类型 | 说明 |
|------|------|------|
| filename | `str` | 输出文件路径 |
| params_dicts | `dict` | 材质参数字典 |

**返回值**: 无（保存为 .npy 文件）

### `clamp_material_params(params_dicts)`
| 参数 | 类型 | 说明 |
|------|------|------|
| params_dicts | `dict` | 材质参数字典 |

**返回值**: `dict` - 裁剪后的参数字典

## 使用示例
```python
import material_utils

# 将 Falcor 原始参数转换为字典格式
params_dicts = material_utils.raw_params_to_dicts(scene, material_ids, raw_params)

# 优化后裁剪参数到有效范围
params_dicts = material_utils.clamp_material_params(params_dicts)

# 转换回 Falcor 格式
raw_params = material_utils.dicts_to_raw_params(scene, material_ids, params_dicts, original_raw)
```

## 依赖关系
### 导入的模块
- `torch` - PyTorch 张量运算
- `falcor` - Falcor 渲染框架
- `numpy` - 数值计算（用于参数导出）
- `loss.compute_render_loss_L2` - L2 损失函数

## 实现细节
- **Standard 材质参数**: base_color（基础颜色）、metallic（金属度）、roughness（粗糙度）
- **PBRTDiffuse 材质参数**: diffuse（漫反射颜色）
- **PBRTConductor 材质参数**: eta（折射率）、k（消光系数）、roughness（粗糙度）
- 参数布局通过 `falcor.get_material_param_layout()` 获取，包含 offset 和 size 信息
- 裁剪范围：roughness 为 [0.1, 0.9999]，其他参数为 [0.0001, 0.9999]
- 每个材质在原始参数中占据固定大小的连续内存块
