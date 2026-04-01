# ShadingData.slang 源码文档

> 路径: `Source/Falcor/Scene/ShadingData.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

ShadingData.slang 定义了着色所需的命中点数据结构 `ShadingData`。此结构体包含几何属性（位置、法线、切线）、视线方向、纹理坐标、材质信息以及折射率等，是材质系统创建材质实例的输入。

支持可微分渲染（标记为 `IDifferentiable`），部分不参与微分的成员使用 `no_diff` 标记。

## 结构体与接口

### `ShadingData : IDifferentiable`

| 成员 | 类型 | 说明 |
|------|------|------|
| `posW` | `float3` | 世界空间着色命中位置 |
| `V` | `float3` | 视线方向（从着色点指向观察者） |
| `uv` | `float2` | 纹理映射坐标 |
| `frame` | `ShadingFrame` | 世界空间平滑插值着色坐标系（法线未自动翻转） |
| `faceN` | `no_diff float3` | 面法线（始终朝向正面） |
| `tangentW` | `no_diff float4` | 几何切线（xyz）和符号（w），用于正交化 |
| `frontFacing` | `bool` | 是否从正面观察 |
| `curveRadius` | `no_diff float` | 曲线截面半径 |
| `mtl` | `MaterialHeader` | 材质头数据 |
| `materialID` | `uint` | 材质 ID |
| `IoR` | `float` | 正面侧介质折射率 |
| `materialGradOffset` | `uint` | 材质梯度缓冲区偏移 |
| `geometryGradOffset` | `uint` | 几何梯度缓冲区偏移 |
| `threadID` | `uint` | 线程 ID（梯度聚合用） |

### 方法

| 方法签名 | 说明 |
|----------|------|
| `float3 computeRayOrigin(bool viewside = true)` | 计算避免自相交的新光线原点（基于 Ray Tracing Gems 第6章方法） |
| `float3 getOrientedFaceNormal()` | 返回朝向视线方向翻转的面法线 |

## 依赖关系 / import

- `Scene.Material.MaterialData` - 材质数据定义
- `Utils.Geometry.GeometryHelpers` - 几何辅助函数（`computeRayOrigin`）
- `Utils.Math.ShadingFrame` - 着色坐标系
