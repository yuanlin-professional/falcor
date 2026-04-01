# WARDiffPathTracer.py 脚本文档

> 路径: `scripts/WARDiffPathTracer.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本构建一个 WAR（Warped-Area Reparameterization）可微分路径追踪器的渲染管线。它用于可视化可微分渲染的前向微分调试输出，同时生成原始渲染图像和微分图像（通过颜色映射可视化）。该脚本还会加载特定的测试场景。

## 主要功能/类

### `render_graph_WARDiffPathTracer()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的 WAR 可微分路径追踪器渲染图

**功能细节**:
- 创建 `WARDiffPathTracer` 通道：
  - 最大反弹次数为 0（仅直接光照）
  - 每像素 1 个采样
  - 前向微分调试模式（`ForwardDiffDebug`）
  - 微分变量名：`CBOX_BUNNY_TRANSLATION`（兔子模型平移）
- 创建两个 `AccumulatePass`：分别累积原始图像和微分图像
- 创建 `ColorMapPass`：将微分值映射到颜色（范围 -4.0 到 4.0）
- 三个输出：累积微分图像、累积原始图像、颜色映射后的微分图像

## 使用示例
```python
# 在 Falcor/Mogwai 中直接加载该脚本
# 脚本会自动加载 bunny_war_diff_pt.pyscene 测试场景
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- 使用 `SceneBuilderFlags` 禁用材质合并、动态对象 RT 合并和材质优化，以确保可微分渲染的正确性
- 脚本末尾通过 `m.loadScene()` 加载指定的测试场景
- 微分变量可以在 `CBOX_BUNNY_MATERIAL`（材质参数）和 `CBOX_BUNNY_TRANSLATION`（几何位移）之间切换
- ColorMapPass 的范围设置为 [-4.0, 4.0]，关闭自动范围调整
