# MinimalPathTracer.py 脚本文档

> 路径: `scripts/MinimalPathTracer.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本构建一个最小化路径追踪器的渲染图。它使用 VBufferRT 生成可见性缓冲区，然后通过 MinimalPathTracer 进行路径追踪计算，最终经过累积通道和色调映射通道输出最终图像。这是 Falcor 中最简单的路径追踪渲染管线示例。

## 主要功能/类

### `render_graph_MinimalPathTracer()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的最小路径追踪器渲染图

**功能细节**:
- 创建 `VBufferRT` 通道：使用分层采样模式（Stratified），16 个采样点
- 创建 `MinimalPathTracer` 通道：最大反弹次数为 3
- 创建 `AccumulatePass` 通道：单精度模式
- 创建 `ToneMapper` 通道：关闭自动曝光，曝光补偿为 0
- 渲染管线：`VBufferRT → MinimalPathTracer → AccumulatePass → ToneMapper → 输出`

## 使用示例
```python
# 在 Falcor/Mogwai 中直接加载该脚本
# 脚本自动创建并注册渲染图
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- VBufferRT 提供 vbuffer 和 viewW 两个输出，分别传递给 MinimalPathTracer
- AccumulatePass 使用单精度模式（`Single`），在交互性和精度之间取得平衡
- ToneMapper 使用固定曝光设置，适合可控的场景渲染
