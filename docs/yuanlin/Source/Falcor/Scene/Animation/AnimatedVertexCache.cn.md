# AnimatedVertexCache 源码文档

> 路径: `Source/Falcor/Scene/Animation/AnimatedVertexCache.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Animation

## 功能概述

管理基于顶点缓存的动画系统，支持曲线（Curve）和网格（Mesh）两种几何体的顶点动画。通过 GPU 计算着色器在关键帧之间进行顶点插值，并更新相应的包围盒（AABB）。支持线性扫掠球体（LSS）和多边形管道（PolyTube）两种曲线细分模式。

## 类与接口

### `CachedCurve`（辅助结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `tessellationMode` | `CurveTessellationMode` | 曲线细分模式 |
| `geometryID` | `CurveOrMeshID` | 关联的几何体 ID |
| `timeSamples` | `vector<double>` | 时间采样点 |
| `indexData` | `vector<uint32_t>` | 索引数据（所有帧共享拓扑） |
| `vertexData` | `vector<vector<DynamicCurveVertexData>>` | 每帧的顶点数据 |

### `CachedMesh`（辅助结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `meshID` | `MeshID` | 关联的网格 ID |
| `timeSamples` | `vector<double>` | 时间采样点 |
| `vertexData` | `vector<vector<PackedStaticVertexData>>` | 每帧的顶点数据 |

### `AnimatedVertexCache`

- **职责**: 管理曲线和网格的顶点缓存动画，在 GPU 上执行关键帧插值

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AnimatedVertexCache(ref<Device>, Scene*, const ref<Buffer>&, vector<CachedCurve>&&, vector<CachedMesh>&&)` | 构造函数，初始化缓冲区和计算着色器 |
| `bool animate(RenderContext*, double time)` | 执行动画更新，返回是否有变更 |
| `void copyToPrevVertices(RenderContext*)` | 将当前顶点复制到前帧缓冲区 |
| `bool hasAnimations() const` | 是否包含任何动画 |
| `bool hasCurveAnimations() const` | 是否包含曲线动画 |
| `bool hasMeshAnimations() const` | 是否包含网格动画 |
| `double getGlobalAnimationLength() const` | 获取全局动画时长 |
| `void setIsLooped(bool)` | 设置循环模式 |
| `void setPreInfinityBehavior(Animation::Behavior)` | 设置第一帧前的行为 |
| `ref<Buffer> getPrevCurveVertexData() const` | 获取前帧曲线顶点缓冲区 |
| `uint64_t getMemoryUsageInBytes() const` | 获取 GPU 内存使用量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpCurveVertexUpdatePass` | `ref<ComputePass>` | 曲线 LSS 顶点更新计算着色器 |
| `mpCurveAABBUpdatePass` | `ref<ComputePass>` | 曲线 AABB 更新计算着色器 |
| `mpCurvePolyTubeVertexUpdatePass` | `ref<ComputePass>` | 曲线 PolyTube 顶点更新计算着色器 |
| `mpMeshVertexUpdatePass` | `ref<ComputePass>` | 网格顶点更新计算着色器 |
| `mCurveKeyframeTimes` | `vector<double>` | 合并后的曲线关键帧时间列表 |

## 依赖关系

### 本文件引用

- `Animation.h` — 动画基类和行为枚举
- `SharedTypes.slang` — 共享类型（InterpolationInfo 等）
- `Core/API/Buffer.h` — GPU 缓冲区
- `Core/Pass/ComputePass.h` — 计算着色器通道
- `Scene/Curves/CurveConfig.h` — 曲线配置
- `Scene/Scene.h` — 场景数据访问

### 被以下文件引用

- `AnimationController.h` — 动画控制器持有此对象

## 实现细节

- 所有曲线缓存的时间戳会合并为统一列表，缺失帧通过线性插值生成
- 网格动画的每个网格可以有独立的时间采样和关键帧数量
- 插值信息（`InterpolationInfo`）包含两个关键帧索引和插值因子 t
- LSS 曲线更新分两步：先更新顶点位置，再更新程序化图元的 AABB
- PolyTube 曲线更新涉及沿切线方向旋转法线，通过四元数旋转实现
- 支持 `Constant` 和 `Cycle` 两种前/后无穷行为
- 计算着色器的 `CURVE_KEYFRAME_COUNT` / `MESH_KEYFRAME_COUNT` 宏在运行时确定
