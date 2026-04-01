# VertexData.slang 源码文档

> 路径: `Source/Falcor/Scene/VertexData.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene

## 功能概述

VertexData.slang 定义了 `VertexData` 结构体，表示在世界空间中插值后的顶点属性。这是光栅化和光线追踪管线中从几何体获取着色信息的标准中间格式。

注意：切线不保证与法线正交，正交化在 `prepareShadingData()` 中完成。

## 结构体与接口

### `VertexData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `posW` | `float3` | 世界空间位置 |
| `normalW` | `float3` | 世界空间着色法线（已归一化） |
| `tangentW` | `float4` | 世界空间着色切线（xyz 已归一化，w 为 +-1.0 或 0 表示无效） |
| `texC` | `float2` | 纹理坐标 |
| `faceNormalW` | `float3` | 世界空间面法线（已归一化） |
| `curveRadius` | `float` | 曲线截面半径（仅曲线几何有效） |
| `coneTexLODValue` | `float` | 光线锥纹理 LOD 值（仅 `getVertexDataRayCones()` 使用时非零） |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` - 主机/设备共享宏
