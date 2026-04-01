# SDFEditor.py 脚本文档

> 路径: `scripts/sdf-editor/SDFEditor.py`
> 类型: Python 脚本
> 模块: scripts/sdf-editor

## 功能概述
该脚本构建一个集成 SDF（Signed Distance Field，有符号距离场）编辑器的渲染管线。它将路径追踪渲染与 SDF 编辑器通道结合，允许用户在渲染场景中交互式编辑 SDF 几何体。渲染管线使用 GBufferRT 生成几何缓冲区，PathTracer 进行光照计算，最终通过 SDFEditor 通道叠加编辑界面。

## 主要功能/类

### `render_graph_DefaultRenderGraph()`
| 参数 | 类型 | 说明 |
|------|------|------|
| 无 | - | - |

**返回值**: `RenderGraph` - 配置好的 SDF 编辑器渲染图

**功能细节**:
- **GBufferRT**：
  - 采样模式：Center
  - 16 个采样点
  - 启用 alpha 测试和着色法线调整
  - 背面剔除模式
  - 纹理 LOD：Mip0
- **PathTracer**：每像素 1 个采样
- **AccumulatePass**：单精度累积，启用自动重置
- **ToneMapper**：ACES 色调映射，固定曝光，启用裁剪
- **SDFEditor**：SDF 编辑器通道

**渲染管线**:
```
GBufferRT ──→ PathTracer ──→ AccumulatePass ──→ ToneMapper ──→ SDFEditor ──→ 输出
    │                                                              ↑
    └──── vbuffer, linearZ ────────────────────────────────────────┘
```

## 使用示例
```python
# 在 Falcor/Mogwai 中加载该脚本
# SDFEditor 提供交互式 SDF 编辑界面
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架 Python 绑定

## 实现细节
- SDFEditor 接收三个输入：vbuffer（用于射线查询）、linearZ（用于深度测试）、inputColor（渲染结果叠加）
- ToneMapper 使用 ACES 色调映射算子，白色最大亮度为 1.0，白色缩放为 11.2
- GBufferRT 将 vbuffer 同时传递给 PathTracer 和 SDFEditor
- GBufferRT 的 viewW 和 mvecW 传递给 PathTracer 用于光照计算
- AccumulatePass 启用 autoReset，在场景变化时自动重置累积
