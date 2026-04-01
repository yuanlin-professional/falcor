# PathTracerNRD.py 脚本文档

> 路径: `scripts/PathTracerNRD.py`
> 类型: Python 脚本
> 模块: scripts

## 功能概述
该脚本构建一个集成了 NVIDIA Real-time Denoisers (NRD) 的路径追踪渲染管线。它同时提供两条渲染路径：一条是传统的累积参考路径，另一条是通过 NRD 进行实时降噪的路径（结合 DLSS 超分辨率）。该管线支持漫反射/镜面反射分离降噪、Delta 反射/透射降噪以及运动向量计算。

## 主要功能/类

### `render_graph_PathTracerNRD()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的 NRD 路径追踪渲染图

**功能细节**:
- **GBufferRT**：Halton 采样模式，32 个采样点，启用 alpha 测试
- **PathTracer**：每像素 1 采样，最大 10 次表面反弹，启用俄罗斯轮盘
- **参考路径**：`PathTracer → AccumulatePass → ToneMapperReference`
- **NRD 路径**：
  - `NRDDiffuseSpecular`：漫反射/镜面反射降噪（ReblurDiffuseSpecular）
  - `NRDDeltaReflection`：Delta 反射降噪（RelaxDiffuse）
  - `NRDDeltaTransmission`：Delta 透射降噪（RelaxDiffuse）
  - `NRDReflectionMotionVectors`：镜面反射运动向量计算
  - `NRDTransmissionMotionVectors`：Delta 透射运动向量计算
  - `ModulateIllumination`：光照调制合成
  - `DLSS`：深度学习超采样
  - `ToneMapperNRD`：色调映射

## 使用示例
```python
# 在 Falcor/Mogwai 中加载，提供两个输出：
# - ToneMapperNRD.dst: NRD 降噪 + DLSS 超分后的结果
# - ToneMapperReference.dst: 参考累积渲染结果
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- NRD 配置了最大亮度限制（250.0）以避免降噪伪影
- Delta 反射和透射使用 RelaxDiffuse 方法进行降噪
- 运动向量使用屏幕空间模式（`worldSpaceMotion: False`）
- DLSS 配置为平衡模式（Balanced），使用相对运动向量缩放和 HDR 输入
- ModulateIllumination 负责将降噪后的各分量（漫反射、镜面反射、Delta 反射/透射、发射光、残余辐射）合成为最终图像
- 渲染图有两个标记输出，可同时查看降噪结果和参考结果进行对比
