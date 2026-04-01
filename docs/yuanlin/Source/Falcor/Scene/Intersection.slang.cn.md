# Intersection.slang 源码文档

> 路径: `Source/Falcor/Scene/Intersection.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

提供光线与各种非三角形几何体的相交测试辅助工具。包含三种相交器（Intersector）：位移三角形网格、曲线和 SDF 网格，在内联光线追踪流程中被调用以处理程序化几何体（Procedural Geometry）的相交判定。

## 结构体与接口

### `DisplacedTriangleMeshIntersector`

位移三角形网格相交器，处理带有位移贴图的三角形网格。

| 方法 | 说明 |
|------|------|
| `static bool intersect(Ray, GeometryInstanceID, uint primitiveIndex, out Attribs, out float t)` | 测试光线与位移三角形的相交，输出重心坐标和位移偏移 |

### `CurveIntersector`

曲线相交器，使用线性扫掠球体（Linear Swept Sphere）模型进行光线-曲线相交测试。

| 方法 | 说明 |
|------|------|
| `static bool intersect(Ray, GeometryInstanceID, uint primitiveIndex, out Attribs, out float t)` | 测试光线与曲线段的相交，支持球形端点连接和背面剔除 |

### `SDFGridIntersector`

SDF 网格相交器，将光线从世界空间变换到局部空间后，调用 SDF 网格的相交方法。

| 方法 | 说明 |
|------|------|
| `static bool intersect(Ray, GeometryInstanceID, uint primitiveID, out SDFGridHitData, out float t)` | 测试光线与 SDF 网格的完整相交 |
| `static bool intersectAny(Ray, GeometryInstanceID, uint primitiveID)` | 测试光线与 SDF 网格的任意命中（可见性光线优化） |

## 函数

各结构体的静态 `intersect` / `intersectAny` 方法如上所述。

## 依赖关系 / import

- `Scene.Scene` - 场景数据访问（`gScene`）
- `Scene.Displacement.DisplacementMapping` - 位移映射
- `Scene.SDFs.SDFGrid` - SDF 网格
- `Utils.Geometry.IntersectionHelpers` - 相交测试辅助函数
- `Utils.Geometry.GeometryHelpers` - 几何辅助函数
- `Utils.Math.Ray`（导出） - 光线定义
- `Utils.Math.MatrixUtils` - 矩阵工具
