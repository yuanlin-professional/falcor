# SceneRayQueryInterface.slang 源码文档

> 路径: `Source/Falcor/Scene/SceneRayQueryInterface.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

定义 `ISceneRayQuery` 接口，为着色器中的光线追踪查询提供统一抽象。渲染算法可以通过此接口进行光线追踪，而不必关心底层是使用 DXR 1.0 的 TraceRay 还是 DXR 1.1 的 RayQuery 内联追踪。

## 结构体与接口

### `interface ISceneRayQuery`

场景光线查询接口。

| 方法 | 说明 |
|------|------|
| `HitInfo traceRay(const Ray ray, out float hitT, uint rayFlags, uint instanceInclusionMask)` | 追踪最近命中光线，返回命中信息 |
| `bool traceVisibilityRay(const Ray ray, uint rayFlags, uint instanceInclusionMask)` | 追踪可见性光线，返回两点是否互相可见 |

## 依赖关系 / import

- `Scene.HitInfo`（导出） - 命中信息类型
- `Utils.Math.Ray`（导出） - 光线定义
