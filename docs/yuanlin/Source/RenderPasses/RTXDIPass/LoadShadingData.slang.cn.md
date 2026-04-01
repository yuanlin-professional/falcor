# LoadShadingData.slang 源码文档

> 路径: `Source/RenderPasses/RTXDIPass/LoadShadingData.slang`
> 类型: Slang 着色器模块
> 模块: RenderPasses/RTXDIPass

## 功能概述

该 Slang 模块提供了从可见性缓冲区（VBuffer）加载和准备着色数据的辅助函数。是 RTXDI 渲染通道中 `PrepareSurfaceData` 和 `FinalShading` 着色器共享的基础模块。

## 结构体与接口

无自定义结构体。通过 `__exported import` 导出场景相关类型供引用者使用。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint getMaterialInstanceHints()` | 根据编译宏 `GBUFFER_ADJUST_SHADING_NORMALS` 返回材质实例提示标志 |
| `bool loadShadingData(uint2 pixel, uint2 frameDim, Camera camera, Texture2D<PackedHitInfo> vbuffer, out ShadingData sd)` | 从 VBuffer 加载指定像素的着色数据，返回是否为有效表面像素 |

### `loadShadingData` 详细说明

**参数**:
- `pixel`: 当前像素坐标
- `frameDim`: 帧尺寸（像素）
- `camera`: 当前相机
- `vbuffer`: 可见性缓冲区纹理
- `sd`: [输出] 着色数据结构体

**返回值**: 像素是否包含有效的表面数据（非背景像素）。注意：`sd.V`（视线方向）始终有效。

## 依赖关系/import

| 模块 | 导出方式 | 说明 |
|------|----------|------|
| `Scene.Scene` | `__exported import` | 场景数据（导出给引用者） |
| `Scene.Shading` | `__exported import` | 着色工具（导出给引用者） |
| `Scene.HitInfo` | `__exported import` | 命中信息（导出给引用者） |
| `Scene.Material.ShadingUtils` | `import` | 着色工具函数 |
| `Utils.Math.MathHelpers` | `import` | 数学辅助函数 |

## 实现细节

1. **VBuffer 解码**: 从 `PackedHitInfo` 格式解码命中信息，仅处理三角形命中类型（`HitType::Triangle`）。

2. **着色数据构建**: 对于有效的三角形命中，获取顶点数据和材质 ID，然后通过 `gScene.materials.prepareShadingData` 构建完整的着色数据。

3. **视线方向**: 使用针孔相机模型 `camera.computeRayPinhole` 计算光线方向，取反后作为视线方向 `sd.V`。

4. **着色法线调整**: `getMaterialInstanceHints()` 检查 `GBUFFER_ADJUST_SHADING_NORMALS` 宏，返回是否需要调整着色法线的提示标志。
