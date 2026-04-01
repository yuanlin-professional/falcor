# BSDFViewer.py 脚本文档

> 路径: `scripts/BSDFViewer.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本用于构建 BSDF（双向散射分布函数）查看器的渲染图。它创建一个简单的渲染管线，将 BSDFViewer 渲染通道的输出连接到累积通道（AccumulatePass），以双精度模式对渲染结果进行逐帧累积，从而获得高质量的 BSDF 可视化效果。该脚本主要用于调试和查看材质的 BSDF 属性。

## 主要功能/类

### `render_graph_BSDFViewer()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的 BSDF 查看器渲染图

**功能细节**:
- 创建 `BSDFViewer` 通道，默认查看 materialID 为 0 的材质
- 创建 `AccumulatePass` 通道，启用双精度模式（`Double`）
- 将 BSDFViewer 的输出连接到累积通道的输入
- 标记累积通道的输出为渲染图的最终输出

## 使用示例
该脚本作为 Falcor 渲染图脚本直接加载：
```python
# 在 Falcor 中加载该脚本即可使用
# 脚本会自动创建渲染图并添加到 Mogwai (m) 中
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定，提供 RenderGraph、createPass 等核心功能

## 实现细节
- 使用 `try/except` 包裹 `m.addGraph()` 调用，以便脚本在 Mogwai 环境外也能被导入而不报错
- AccumulatePass 使用双精度模式以确保 BSDF 查看结果的数值精度
- 渲染管线结构：`BSDFViewer → AccumulatePass → 输出`
