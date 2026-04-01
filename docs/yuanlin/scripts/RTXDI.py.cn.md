# RTXDI.py 脚本文档

> 路径: `scripts/RTXDI.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本构建一个使用 RTXDI（RTX Direct Illumination）技术的渲染管线。RTXDI 是 NVIDIA 的实时直接光照采样技术，能够在包含大量光源的场景中实现高效的直接光照计算。管线包括可见性缓冲区生成、RTXDI 光照计算、帧累积和色调映射。

## 主要功能/类

### `render_graph_RTXDI()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的 RTXDI 渲染图

**功能细节**:
- 创建 `VBufferRT` 通道：使用默认参数
- 创建 `RTXDIPass` 通道：使用默认参数
- 创建 `AccumulatePass` 通道：默认**禁用**累积（`enabled: False`）
- 创建 `ToneMapper` 通道：固定曝光
- 连接：VBufferRT 输出 vbuffer 和 mvec 到 RTXDIPass
- 渲染管线：`VBufferRT → RTXDIPass → AccumulatePass → ToneMapper → 输出`

## 使用示例
```python
# 在 Falcor/Mogwai 中直接加载该脚本
# 适用于包含大量光源的场景
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- AccumulatePass 默认禁用，意味着默认以单帧模式运行，适合实时交互
- RTXDIPass 需要 vbuffer 和 mvec（运动向量）输入，后者用于时序重用
- RTXDI 技术通过重要性重采样（ReSTIR）算法实现高效的光源采样
