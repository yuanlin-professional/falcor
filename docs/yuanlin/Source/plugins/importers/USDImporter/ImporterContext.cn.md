# ImporterContext 源码文档

> 路径: `Source/plugins/importers/USDImporter/ImporterContext.h`, `ImporterContext.cpp`
> 类型: C++ 头文件 + 实现
> 模块: plugins/importers/USDImporter

## 功能概述

USD 导入器的核心上下文类，管理整个导入会话的状态和数据。负责 USD 场景的遍历、对象创建（网格、曲线、灯光、相机、骨骼、动画、材质）、变换层级管理、以及最终的场景组装（`finalize`）。

## 类与接口

### `GeomInstance`

- **职责**: 表示 `UsdGeomGprim` 的一个实例（网格或曲线）。

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `string` | 实例名称 |
| `prim` | `UsdPrim` | 引用的 prim |
| `xform` | `float4x4` | 世界变换 |
| `bindTransform` | `float4x4` | 蒙皮绑定变换 |
| `parentID` | `NodeID` | 父节点 ID |

### `PrototypeInstance`

- **职责**: 表示原型（prototype）的一个实例。

### `Mesh`

- **职责**: 表示 USD 场景中的一个网格，包含处理后的网格数据、动画时间采样、缓存数据等。

### `Curve`

- **职责**: 表示 USD 场景中的曲线，支持不同的细分模式。

### `PrototypeGeom`

- **职责**: 表示一个可实例化的原型场景子图，包含节点层级、几何实例和动画。

### `Skeleton`

- **职责**: 表示 USD 骨骼系统（`SkelRoot` + `Skeleton` + `SkelAnimation`），管理骨骼节点、蒙皮映射和动画数据。

### `ImporterContext`

- **职责**: 导入会话的核心上下文，管理所有导入数据和操作。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ImporterContext(path, UsdStageRefPtr, SceneBuilder&, ...)` | 构造函数，初始化导入上下文 |
| `ref<Material> getDefaultMaterial(const UsdPrim&)` | 获取/创建默认材质（线程安全） |
| `ref<Material> resolveMaterial(...)` | 解析 prim 绑定的材质（线程安全） |
| `void addMesh(const UsdPrim&)` | 添加网格到处理队列 |
| `void addGeomInstance(...)` | 添加几何实例 |
| `void createPrototype(const UsdPrim&)` | 创建原型几何体 |
| `void addCurve(const UsdPrim&)` | 添加曲线 |
| `void createEnvMap/createDistantLight/createRectLight/...` | 创建各类灯光 |
| `bool createCamera(const UsdPrim&)` | 创建相机 |
| `void createPointInstances(const UsdPrim&)` | 创建点实例化 |
| `void createSkeleton(const UsdPrim&)` | 创建骨骼 |
| `NodeID createAnimation(const UsdGeomXformable&)` | 从时间采样变换创建动画 |
| `void setRootXform(const float4x4&)` | 设置根变换（单位/朝向校正） |
| `void pushNode/popNode` | 节点栈操作 |
| `void pushNodeStack/popNodeStack` | 节点栈的栈操作（支持嵌套子图） |
| `void finalize()` | 场景遍历完成后的最终处理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `pStage` | `UsdStageRefPtr` | USD Stage |
| `builder` | `SceneBuilder&` | Falcor 场景构建器 |
| `meshes` | `vector<Mesh>` | 网格列表 |
| `curves` | `vector<Curve>` | 曲线列表 |
| `skeletons` | `vector<Skeleton>` | 骨骼列表 |
| `geomInstances` | `vector<GeomInstance>` | 几何实例列表 |
| `prototypeGeoms` | `vector<PrototypeGeom>` | 原型几何体列表 |
| `prototypeInstances` | `vector<PrototypeInstance>` | 原型实例列表 |
| `materialMap` | `unordered_map<string, ref<Material>>` | 材质缓存 |
| `mpPreviewSurfaceConverter` | `unique_ptr<PreviewSurfaceConverter>` | UsdPreviewSurface 转换器 |
| `metersPerUnit` | `float` | 场景单位（默认 0.01，即厘米） |
| `timeCodesPerSecond` | `double` | 时间码每秒（默认 24） |
| `defaultRefinementLevel` | `int` | 默认细分精化等级 |
| `rootXform` | `float4x4` | 根变换 |

## 依赖关系

### 本文件引用

- `Scene/SceneBuilder.h`、`SceneIDs.h` - 场景构建
- `Scene/Animation/Animation.h` - 动画
- `Scene/Curves/CurveTessellation.h` - 曲线细分
- `USDUtils/USDUtils.h`、`USDHelpers.h` - USD 工具
- `USDUtils/PreviewSurfaceConverter/PreviewSurfaceConverter.h` - 材质转换
- USD API（`UsdGeom`、`UsdShade`、`UsdSkel` 等）

### 被以下文件引用

- `USDImporter.cpp` - 作为导入会话的核心对象

## 实现细节

- 节点栈支持嵌套（stack of stacks），通过 `pushNodeStack`/`popNodeStack` 管理，用于原型子图独立的节点层级。
- 材质解析优先使用 `UsdShadeMaterialBindingAPI`，并利用 `BindingsCache` 和 `CollectionQueryCache` 加速。
- 网格处理分为非时间采样任务（`meshTasks`）和关键帧任务（`meshKeyframeTasks`），支持并行处理。
- 骨骼系统映射为 SceneBuilder 的节点层级和动画关键帧。
- 点实例化（`UsdGeomPointInstancer`）支持动画变换导出。
