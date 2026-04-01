# SceneIDs 源码文档

> 路径: `Source/Falcor/Scene/SceneIDs.h`
> 类型: C++ 头文件
> 模块: Scene

## 功能概述

SceneIDs.h 定义了场景中各种对象的强类型 ID（使用 `ObjectID` 模板），确保不同类型的 ID 不会被混淆。所有 ID 类型定义在 `Falcor::scene1` 命名空间中，并通过 `using namespace` 导入到 `Falcor` 命名空间。

## 类与接口

### `SceneObjectKind` 枚举

场景对象种类枚举，作为 `ObjectID` 模板的区分参数。

| 枚举值 | 说明 |
|--------|------|
| `kNode` | 场景图节点 / 矩阵 ID |
| `kMesh` | 网格 ID |
| `kCurve` | 曲线 ID |
| `kCurveOrMesh` | 曲线或网格 ID（根据细分模式而定） |
| `kSdfDesc` | SDF 描述 ID（用户面向） |
| `kSdfGrid` | SDF 网格 ID（内部，可去重） |
| `kMaterial` | 材质 ID |
| `kLight` | 光源 ID |
| `kCamera` | 相机 ID |
| `kVolume` | 体积 ID |
| `kGlobalGeometry` | 全局几何 ID（线性化顺序：网格、曲线、SDF、自定义） |

### ID 类型别名

| 类型 | 说明 |
|------|------|
| `NodeID` | 场景图节点 ID |
| `MeshID` | 网格 ID |
| `CurveID` | 曲线 ID |
| `CurveOrMeshID` | 曲线或网格 ID |
| `SdfDescID` | SDF 描述 ID |
| `SdfGridID` | SDF 网格 ID |
| `MaterialID` | 材质 ID |
| `LightID` | 光源 ID |
| `CameraID` | 相机 ID |
| `VolumeID` | 体积 ID |
| `GlobalGeometryID` | 全局几何 ID |

## 依赖关系

### 本文件引用

- `Utils/ObjectID.h` - `ObjectID` 强类型 ID 模板

### 被以下文件引用

- `Scene.h` - 使用各种 ID 类型
- `SceneBuilder.h` - 添加对象时返回 ID
- 场景子系统中广泛使用
