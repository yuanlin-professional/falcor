# SharedTypes.slang 源码文档

> 路径: `Source/Falcor/Scene/Animation/SharedTypes.slang`
> 类型: Slang 着色器文件（主机/设备共享结构）
> 模块: Scene/Animation

## 功能概述

定义动画系统中 CPU 和 GPU 共享的数据结构，包括插值信息、曲线元数据和网格元数据。这些结构体在计算着色器中用于顶点缓存动画的关键帧插值。

## 结构体与接口

### `InterpolationInfo`

| 成员 | 类型 | 说明 |
|------|------|------|
| `keyframeIndices` | `uint2` | 两个相邻关键帧的索引（x = 前帧，y = 后帧） |
| `t` | `float` | 插值因子（0.0 ~ 1.0） |

### `PerCurveMetadata`

| 成员 | 类型 | 说明 |
|------|------|------|
| `indexCount` | `uint` | 曲线索引数量 |
| `indexOffset` | `uint` | 曲线索引在全局缓冲区中的偏移 |
| `vertexCount` | `uint` | 曲线顶点数量 |
| `vertexOffset` | `uint` | 曲线顶点在全局缓冲区中的偏移 |

### `PerMeshMetadata`

| 成员 | 类型 | 说明 |
|------|------|------|
| `keyframeBufferOffset` | `uint` | 关键帧缓冲区中的起始偏移（输入） |
| `sceneVbOffset` | `uint` | 场景顶点缓冲区中的偏移（输出） |
| `prevVbOffset` | `uint` | 前帧顶点缓冲区中的偏移（输出） |
| `vertexCount` | `uint` | 网格顶点数量 |

## 依赖关系

### import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏
