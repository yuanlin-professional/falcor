# LightCollection 源码文档

> 路径: `Source/Falcor/Scene/Lights/LightCollection.h`, `Source/Falcor/Scene/Lights/LightCollection.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Lights

## 功能概述

场景中自发光网格光源集合的具体实现。负责收集场景中所有使用自发光材质的网格实例，构建自发光三角形列表，预积分纹理化自发光的平均辐照度和通量，并在动画期间更新顶点位置。该类是自发光采样器的数据提供者。

## 类与接口

### `LightCollection`

- **继承**: `ILightCollection`
- **职责**: 管理场景中所有自发光三角形的几何数据、通量数据和活跃三角形列表。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<LightCollection> create(...)` | 创建光源集合 |
| `bool update(RenderContext*, UpdateStatus*)` | 检测变换变化并更新三角形位置 |
| `void bindShaderData(const ShaderVar&) const` | 绑定所有缓冲区到着色器 |
| `uint32_t getTotalLightCount() const` | 返回三角形总数 |
| `const MeshLightStats& getStats(RenderContext*) const` | 计算并返回统计数据 |
| `const vector<MeshLightTriangle>& getMeshLightTriangles(RenderContext*) const` | 同步 CPU 数据并返回三角形列表 |
| `void prepareSyncCPUData(RenderContext*) const` | 预调度 GPU 到 CPU 数据拷贝 |
| `uint64_t getMemoryUsageInBytes() const` | 计算所有 GPU 缓冲区的总内存 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `Scene*` | 所属场景（非持有指针） |
| `mMeshLights` | `vector<MeshLightData>` | 所有网格光源数据 |
| `mTriangleCount` | `uint32_t` | 自发光三角形总数 |
| `mpTriangleData` | `ref<Buffer>` | GPU 端三角形几何数据缓冲区 |
| `mpFluxData` | `ref<Buffer>` | GPU 端三角形通量数据缓冲区 |
| `mpActiveTriangleList` | `ref<Buffer>` | 活跃三角形索引列表 |
| `mpPerMeshInstanceOffset` | `ref<Buffer>` | 实例 ID 到三角形偏移的查找表 |
| `mIntegrator` | 结构体 | 自发光纹理积分器（光栅化管线） |

## 依赖关系

### 本文件引用

- `ILightCollection.h` — 接口基类
- `MeshLightData.slang`, `LightCollectionShared.slang` — 数据结构
- `Scene/Scene.h`, `Scene/Material/BasicMaterial.h` — 场景和材质访问
- `EmissiveIntegrator.3d.slang` — 纹理积分着色器
- `BuildTriangleList.cs.slang` — 三角形列表构建计算着色器
- `UpdateTriangleVertices.cs.slang` — 顶点位置更新计算着色器
- `FinalizeIntegration.cs.slang` — 积分结果计算着色器

### 被以下文件引用

- `Scene/Scene.h` — 场景持有光源集合
- 各种自发光采样器实现

## 实现细节

- **纹理积分流程**：使用三通道光栅化管线将自发光三角形投射到纹理空间。
  1. 第一遍：找到每个三角形的最大纹素值（用于定点数缩放）。
  2. 第二遍：使用保守光栅化和 64 位原子操作累加纹素值（定点格式，保证确定性结果）。
  3. 第三遍（计算着色器）：计算最终的平均辐照度和通量。
- **活跃三角形列表**：通量为零的三角形被剔除，只有活跃三角形参与采样。
- **CPU 数据同步**：使用暂存缓冲区（Staging Buffer）和围栏（Fence）异步回读 GPU 数据。
- 要求设备支持保守光栅化 Tier 3 和 Shader Model 6.6。
