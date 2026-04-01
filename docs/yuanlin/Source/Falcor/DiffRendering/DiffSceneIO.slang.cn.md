# DiffSceneIO.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/DiffSceneIO.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件实现了可微场景 I/O 包装器 `DiffSceneIO`，负责以可微方式从场景中加载顶点数据（位置、法线、切线）和相机位置。它为每种数据类型提供了自定义的前向导数（ForwardDerivative）和反向导数（BackwardDerivative）函数，使得梯度能够正确传播到网格顶点位置、法线、切线等场景参数。

核心设计思路：在前向传播中从场景加载原始顶点数据（非可微），在反向传播中通过原子操作将梯度写入到场景梯度缓冲区。支持四种 DIFF_MODE 编译模式：原始模式(0)、反向微分(1)、前向调试(2)、反向调试(3)。

## 结构体与接口

### `DiffSceneIO`

- **职责**: 可微场景数据加载器，封装顶点位置/法线/切线的可微读取和梯度回传逻辑

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint3 getVtxIndices(GeometryInstanceID, uint triangleID)` | 获取三角形的局部顶点索引 |
| `void loadVertexPositionsW(SceneGradientInfo, GeometryInstanceID, uint, out float3[3])` | 加载世界空间顶点位置（带自定义前向/反向导数） |
| `void fwd_loadVertexPositionsW(...)` | 顶点位置的前向导数函数，用于前向调试模式可视化 |
| `void bwd_loadVertexPositionsW(...)` | 顶点位置的反向导数函数，将梯度原子累加到 `MeshPosition` 梯度缓冲区 |
| `void loadVertexNormalsW(SceneGradientInfo, GeometryInstanceID, uint, out float3[3])` | 加载世界空间顶点法线（带自定义导数） |
| `void fwd_loadVertexNormalsW(...)` | 顶点法线的前向导数函数 |
| `void bwd_loadVertexNormalsW(...)` | 顶点法线的反向导数函数，梯度写入 `MeshNormal` 缓冲区 |
| `void loadVertexTangentsW(SceneGradientInfo, GeometryInstanceID, uint, out float3[3])` | 加载世界空间顶点切线（带自定义导数） |
| `void fwd_loadVertexTangentsW(...)` | 顶点切线的前向导数函数 |
| `void bwd_loadVertexTangentsW(...)` | 顶点切线的反向导数函数，梯度写入 `MeshTangent` 缓冲区 |
| `[Differentiable] float3 loadCameraPositionW(SceneGradientInfo)` | 加载相机世界空间位置 |

## 全局变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gOutputDColor` | `RWTexture2D<float4>` | 反向调试模式下输出梯度图像的纹理 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Scene/SceneDefines.slangh` | 场景宏定义 |
| `Utils/Math/MathConstants.slangh` | 数学常量 |
| `Scene.Scene` | 场景主模块 |
| `Scene.SceneTypes` | 场景类型定义 |
| `DiffRendering.SceneGradientInfo` | 导出的场景梯度信息（`__exported`） |
| `DiffRendering.DiffDebugParams` | 调试参数 |
| `DiffRendering.InverseOptimizationParams` | 逆优化参数 |
| `DiffRendering.SceneGradients` | 场景梯度缓冲区 |
| `DiffRendering.GradientIOWrapper` | 梯度 I/O 包装器 |

### 被以下文件引用

- `DiffRendering/DiffSceneQuery.slang` — 通过 `SceneQueryAD` 使用本模块进行可微场景查询
