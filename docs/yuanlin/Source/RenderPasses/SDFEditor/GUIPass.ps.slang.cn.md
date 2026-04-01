# GUIPass.ps.slang 源码文档

> 路径: `Source/RenderPasses/SDFEditor/GUIPass.ps.slang`
> 类型: 像素着色器 (Pixel Shader)
> 模块: RenderPasses/SDFEditor

## 功能概述

SDF 编辑器的全屏 GUI 像素着色器。在输入颜色图像上叠加渲染包围盒线框、网格平面、对称平面、编辑图元预览和 2D GUI 标记。同时在鼠标位置写入拾取信息供 CPU 端回读。

## 结构体与接口

### `GUIPass`

GUI 渲染参数结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `resolution` | `uint2` | 分辨率 |
| `mousePos` | `uint2` | 鼠标位置 |
| `markerSet` | `Marker2DSet` | 2D 标记集 |
| `pickingData` | `RWStructuredBuffer<SDFPickingInfo>` | 拾取信息输出 |
| `editingPrimitiveData` | `StructuredBuffer<SDFEditingData>` | 编辑图元数据 |
| `gridInstanceIDs` | `Buffer<uint>` | SDF 网格实例 ID 列表 |
| `bbRenderSettings` | `SDFBBRenderSettings` | 包围盒渲染设置 |
| `gridInstanceCount` | `uint` | 网格实例数量 |
| `ui2DActive` | `uint` | 2D UI 是否激活 |
| `gridPlane` | `SDFGridPlane` | 网格平面 |
| `symmetryPlane` | `SDFGridPlane` | 对称平面 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `bool iBoundingBox(float3, float3, float3, float, out float)` | 射线与包围盒线框求交 |
| `bool intersectGridPlane(float3, float3, float3, float3, float3, out float, ...)` | 射线与网格平面求交 |
| `void rayToSDFLocalSpace(uint, inout Ray)` | 将射线变换到 SDF 局部空间 |
| `float4 traceSDFBoundingBox(Ray, uint, float)` | 追踪 SDF 包围盒线框 |
| `bool sdfIntersectPrimitive(float3, float3, float3, float3, SDF3DPrimitive, out float)` | 光线步进求交 SDF 图元 |
| `float4 renderGridPlanes(Ray, HitInfo, float, float4)` | 渲染网格平面和对称平面 |
| `float4 psMain(float2 texC)` | 像素着色器主入口 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `Scene.Scene` — 场景数据
- `Scene.HitInfo` — 命中信息
- `Utils.SDF.SDF3DShapes` — 3D SDF 形状
- `Scene.SDFs.SDF3DPrimitive` — SDF 图元
- `Utils.Geometry.IntersectionHelpers` — 射线相交辅助
- `Scene.RaytracingInline` — 内联光线追踪
- `Utils.Math.AABB`、`Utils.Math.MatrixUtils`
- `Marker2DSet`、`SDFEditorTypes`

## 实现细节

- 着色器首先读取 VBuffer 获取命中信息，在鼠标像素位置写入拾取数据
- 根据包围盒渲染模式，对所有或选中的 SDF 网格实例追踪包围盒线框
- 网格平面通过射线-平面求交和 UV 坐标 frac 实现网格线绘制
- 编辑图元预览通过 SDF 光线步进（128 步）实现，半透明叠加
- 最后渲染 2D GUI 标记集（选择轮、形状指示器等）
