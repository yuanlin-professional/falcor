# Scene.slang 源码文档

> 路径: `Source/Falcor/Scene/Scene.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

Scene.slang 是 GPU 端场景数据的核心定义文件。它定义了 `Scene` 结构体，包含渲染所需的所有场景资源（加速结构、变换矩阵、几何体缓冲区、材质系统、光源、相机、体积等），并提供大量的数据访问和几何计算方法。

全局实例 `gScene` 作为 `ParameterBlock<Scene>` 被所有着色器访问。

## 结构体与接口

### `Scene`

GPU 端场景数据结构，是着色器访问场景的统一入口。

#### 核心资源

| 成员 | 类型 | 说明 |
|------|------|------|
| `rtAccel` | `RaytracingAccelerationStructure` | 顶层加速结构（TLAS） |
| `worldMatrices` | `StructuredBuffer<float4x4>` | 世界变换矩阵 |
| `inverseTransposeWorldMatrices` | `StructuredBuffer<float4x4>` | 逆转置世界矩阵（法线变换） |
| `geometryInstances` | `StructuredBuffer<GeometryInstanceData>` | 几何实例数据 |
| `meshes` | `StructuredBuffer<MeshDesc>` | 网格描述 |
| `vertices` | `SplitVertexBuffer` | 顶点数据（支持超大缓冲区） |
| `materials` | `ParameterBlock<MaterialSystem>` | 材质系统 |
| `lights` | `StructuredBuffer<LightData>` | 光源数据 |
| `camera` | `Camera` | 相机 |
| `envMap` | `EnvMap` | 环境贴图 |

#### 关键方法分类

**变换矩阵访问**

| 方法 | 说明 |
|------|------|
| `getWorldMatrix(instanceID)` | 获取实例的世界矩阵 |
| `getPrevWorldMatrix(instanceID)` | 获取上一帧的世界矩阵 |
| `getInverseTransposeWorldMatrix(instanceID)` | 获取逆转置世界矩阵 |

**几何数据访问**

| 方法 | 说明 |
|------|------|
| `getIndices(instanceID, triangleIndex)` | 获取三角形的全局顶点索引 |
| `getVertex(index)` | 获取解包后的顶点数据 |
| `getVertexData(instanceID, triangleIndex, barycentrics)` | 获取插值后的顶点属性 |
| `getVertexData(TriangleHit)` | 从三角形命中获取顶点属性 |
| `getVertexData(DisplacedTriangleHit, viewDir)` | 从位移三角形命中获取顶点属性 |
| `getVertexDataRayCones(instanceID, triangleIndex, barycentrics)` | 获取含光线锥 LOD 的顶点属性 |
| `getVertexDataFromCurve(CurveHit)` | 从曲线命中获取顶点属性 |
| `getVertexDataFromSDFGrid(SDFGridHit, posW)` | 从 SDF 网格命中获取顶点属性 |

**法线与面积计算**

| 方法 | 说明 |
|------|------|
| `getFaceNormalW(instanceID, triangleIndex)` | 获取世界空间面法线 |
| `getFaceAreaW(instanceID, triangleIndex)` | 获取世界空间三角形面积 |
| `computeFaceNormalAndAreaW(instanceID, p[3], out area)` | 同时计算面法线和面积 |

**前帧位置（运动模糊）**

| 方法 | 说明 |
|------|------|
| `getPrevPosW(HitInfo)` | 多态获取上一帧世界位置 |
| `getPrevPosW(TriangleHit)` | 获取三角形上一帧位置 |
| `getPrevPosWFromCurve(CurveHit)` | 获取曲线上一帧位置 |

**曲率估计**

| 方法 | 说明 |
|------|------|
| `computeCurvatureIsotropic(instanceID, triangleIndex)` | 各向同性曲率估计 |
| `computeCurvatureVisibleEllipse(instanceID, triangleIndex, rayDir, width, angle)` | 可见椭圆曲率估计 |

## 依赖关系 / import

- `SceneDefines.slangh` - 场景几何类型宏
- `Scene.HitInfo`（导出） - 命中信息
- `Scene.SceneTypes`（导出） - 场景类型
- `Scene.Camera.Camera`（导出） - 相机
- `Scene.Lights.*`（导出） - 光源系统
- `Scene.Material.MaterialSystem`（导出） - 材质系统
- `Scene.Volume.GridVolume`（导出） - 体积
- `Scene.SDFs.SDFGrid`（导出） - SDF 网格
- `Utils.Math.*` - 数学工具
- `Utils.Geometry.*` - 几何工具
- `Rendering.Materials.TexLODHelpers` - 纹理 LOD
