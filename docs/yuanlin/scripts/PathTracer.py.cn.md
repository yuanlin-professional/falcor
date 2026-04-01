# PathTracer.py 脚本文档

> 路径: `scripts/PathTracer.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本构建一个完整的路径追踪渲染管线。相比 MinimalPathTracer，它使用功能更完整的 PathTracer 通道，支持更多高级特性（如 alpha 测试、运动向量等）。这是 Falcor 中标准的路径追踪渲染配置。

## 主要功能/类

### `render_graph_PathTracer()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的路径追踪器渲染图

**功能细节**:
- 创建 `PathTracer` 通道：每像素 1 个采样
- 创建 `VBufferRT` 通道：分层采样，16 个采样点，启用 alpha 测试
- 创建 `AccumulatePass` 通道：单精度累积
- 创建 `ToneMapper` 通道：固定曝光
- 连接边：VBufferRT 输出 vbuffer、viewW、mvec 到 PathTracer
- 渲染管线：`VBufferRT → PathTracer → AccumulatePass → ToneMapper → 输出`

## 使用示例
```python
# 在 Falcor/Mogwai 中直接加载该脚本
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- 与 MinimalPathTracer 的主要区别在于使用了完整的 PathTracer 通道，支持运动向量（mvec）等高级特性
- VBufferRT 启用了 `useAlphaTest`，可正确处理透明材质
- 通过 mvec 边连接支持运动模糊和时序降噪等功能
