# ILightCollection 源码文档

> 路径: `Source/Falcor/Scene/Lights/ILightCollection.h`
> 类型: C++ 头文件
> 模块: Scene/Lights

## 功能概述

定义网格光源集合的抽象接口 `ILightCollection`。该接口描述了场景中所有自发光网格（emissive mesh）的管理和访问方式，包括更新、着色器绑定、统计信息和 CPU/GPU 数据同步等功能。

## 类与接口

### `ILightCollection`

- **继承**: `Object`
- **职责**: 网格光源集合的抽象接口，被 `LightCollection` 实现。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual bool update(RenderContext*, UpdateStatus*) = 0` | 更新光源集合到场景当前状态 |
| `virtual void bindShaderData(const ShaderVar&) const = 0` | 绑定数据到着色器变量 |
| `uint32_t getActiveLightCount(RenderContext*) const` | 获取活跃（未剔除）三角形光源数 |
| `virtual uint32_t getTotalLightCount() const = 0` | 获取三角形光源总数 |
| `virtual const MeshLightStats& getStats(RenderContext*) const = 0` | 获取统计信息 |
| `virtual const vector<MeshLightTriangle>& getMeshLightTriangles(RenderContext*) const = 0` | 获取所有自发光三角形（CPU端） |
| `virtual const vector<MeshLightData>& getMeshLights() const = 0` | 获取所有网格光源数据 |
| `virtual void prepareSyncCPUData(RenderContext*) const = 0` | 预调度 CPU 数据同步 |
| `virtual uint64_t getMemoryUsageInBytes() const = 0` | 获取 GPU 内存使用量 |
| `virtual UpdateFlagsSignal::Interface getUpdateFlagsSignal() = 0` | 获取更新信号接口 |

#### 关键成员 / 内部类型

| 类型 | 说明 |
|------|------|
| `UpdateFlags` | 更新标志位：None, MatrixChanged, LayoutChanged |
| `MeshLightStats` | 网格光源统计：光源数、三角形数、纹理化比例等 |
| `MeshLightVertex` | 网格光源三角形顶点（位置 + UV） |
| `MeshLightTriangle` | 网格光源三角形完整数据（顶点、法线、辐照度、通量、面积） |

## 依赖关系

### 本文件引用

- `MeshLightData.slang` — 网格光源数据结构
- `Core/Object.h` — 基类
- `sigs/sigs.h` — 信号系统

### 被以下文件引用

- `LightCollection.h` — 具体实现类
- 自发光光源采样器
