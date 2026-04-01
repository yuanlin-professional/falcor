# SceneDebugger.py 脚本文档

> 路径: `scripts/SceneDebugger.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本构建一个场景调试器渲染管线。SceneDebugger 是 Falcor 中的调试工具，用于可视化场景的各种属性（如法线、UV 坐标、材质 ID、网格 ID 等），帮助开发者检查场景数据的正确性。

## 主要功能/类

### `render_graph_SceneDebugger()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的场景调试器渲染图

**功能细节**:
- 创建 `SceneDebugger` 通道，使用默认参数
- 直接标记 SceneDebugger 的输出为渲染图输出
- 这是所有渲染图脚本中最简单的配置，只有一个通道

## 使用示例
```python
# 在 Falcor/Mogwai 中直接加载该脚本
# 通过 SceneDebugger 的 UI 可以切换不同的调试可视化模式
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- 渲染管线结构极为简单：`SceneDebugger → 输出`
- SceneDebugger 通道内部自行处理场景查询和可视化，无需额外的缓冲区输入
