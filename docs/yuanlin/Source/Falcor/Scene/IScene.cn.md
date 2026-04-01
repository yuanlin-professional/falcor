# IScene 源码文档

> 路径: `Source/Falcor/Scene/IScene.h` + `Source/Falcor/Scene/IScene.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene

## 功能概述

IScene 是场景的抽象接口类，定义了渲染器与场景交互的核心契约。它声明了场景绑定、光线追踪、光照、相机、材质等方面的纯虚方法，使得渲染通道可以不依赖具体的 Scene 实现。

## 类与接口

### `IScene`

- **继承**: `Object`（Falcor 对象基类）
- **职责**: 定义场景的公共接口，包括着色器绑定、光线追踪资源管理、渲染设置等

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual void getShaderDefines(DefineList&) const = 0` | 获取场景所需的着色器宏定义 |
| `virtual void getTypeConformances(TypeConformanceList&, TypeConformancesKind) const = 0` | 获取类型一致性（材质、几何等） |
| `virtual void getShaderModules(ProgramDesc::ShaderModuleList&) const = 0` | 获取场景所需的着色器模块 |
| `virtual void bindShaderData(const ShaderVar&) const = 0` | 绑定场景数据到着色器变量（不含 TLAS） |
| `virtual void bindShaderDataForRaytracing(RenderContext*, const ShaderVar&, uint32_t rayTypeCount) = 0` | 按需创建 TLAS 并绑定光线追踪资源 |
| `virtual const RenderSettings& getRenderSettings() const = 0` | 获取渲染设置 |
| `virtual RtPipelineFlags getRtPipelineFlags() const = 0` | 获取光线追踪管线标志 |
| `virtual const AABB& getSceneBounds() const = 0` | 获取场景包围盒 |
| `virtual bool useEmissiveLights() const = 0` | 是否启用自发光光源 |
| `virtual bool useEnvLight() const = 0` | 是否启用环境光 |
| `virtual ref<ILightCollection> getILightCollection(RenderContext*) = 0` | 获取光源集合 |
| `virtual const ref<Camera>& getCamera() const = 0` | 获取当前相机 |
| `virtual const MaterialSystem& getMaterialSystem() const = 0` | 获取材质系统 |
| `void raytrace(RenderContext*, Program*, const ref<RtProgramVars>&, uint3)` | 便捷光线追踪调度函数（非纯虚，有默认实现） |

### `UpdateFlags` 枚举

场景更新标志位，指示场景中哪些内容发生了变化：

| 标志 | 说明 |
|------|------|
| `GeometryMoved` | 几何体移动 |
| `CameraMoved` | 相机移动 |
| `MaterialsChanged` | 材质变更 |
| `LightCollectionChanged` | 光源集合变更 |
| `SceneGraphChanged` | 场景图变更 |
| `RecompileNeeded` | 需要重新编译着色器（定义/类型一致性/代码变更） |

### `RenderSettings` 结构体

| 成员 | 类型 | 说明 |
|------|------|------|
| `useEnvLight` | `bool` | 启用环境贴图照明 |
| `useAnalyticLights` | `bool` | 启用解析光源 |
| `useEmissiveLights` | `bool` | 启用自发光光源 |
| `useGridVolumes` | `bool` | 启用网格体积渲染 |

## 依赖关系

### 本文件引用

- `Core/Object.h` - 对象基类
- `Core/Program/Program.h` - 着色器程序
- `Core/API/Raytracing.h` - 光线追踪 API
- `sigs/sigs.h` - 信号/槽机制

### 被以下文件引用

- `Scene.h` - Scene 类继承 IScene
- 各渲染通道通过 IScene 接口访问场景

## 实现细节

- `raytrace()` 的默认实现位于 `IScene.cpp` 中，它先调用 `bindShaderDataForRaytracing()` 绑定资源，再通过 `RenderContext::raytrace()` 发起光线追踪调度
- `UpdateFlags` 和 `TypeConformancesKind` 均支持位运算操作符
