### [Index](./index.md) | 已知问题

--------

# 已知问题

## USD 支持

Falcor 的 USD 导入器实现了完整 [USD 规范](https://graphics.pixar.com/usd/release/index.html)的一个子集。以下概述了 Falcor USD 导入器不支持的一些 USD 功能。

### 常规
- 不支持 `UsdPhysics`。
- `UsdVariantSets` 无法正常工作。
- 不支持相机参数（光圈大小等）的动画。
- 仅支持逐顶点蒙皮数据。特别是，不支持常量蒙皮数据。
- 每个顶点最多支持 4 根骨骼。额外的骨骼将被忽略，并发出警告。

### UsdGeom

- 不支持 `UsdGeomCapsule`、`UsdGeomCone`、`UsdGeomCube`、`UsdGeomCylinder`、`UsdGeomHermiteCurves`、`UsdGeomNurbsCurves`、`UsdGeomNurbsPatch` 和 `UsdGeomPoints`
- 不支持 `UsdGeomBasisCurves` 的实例化。
- `DisplayColor` 的插值模式被忽略。
- 不支持枢轴变换。

### UsdLux
- 不支持 `UsdLuxShapingAPI` 和 `UsdLuxShadowAPI`。
- 不支持 `UsdLuxCylinderLight` 和 `UsdLuxPortalLight`。
- 不支持灯光的实例化。

### UsdPreviewSurface

- 仅支持 `metallic` 工作流。当遇到使用 `specular` 工作流的 `UsdPreviewSurface` shader 时，会发出警告。
- 不支持 `UsdPreviewSurface` 的 `clearcoat`、`clearcloatRoughness`、`occlusion` 和 `displacement` 输入。
- 不支持 `UsdUVTexture` 的 `bias`、`scale`、`wrapS` 和 `wrapT` 输入。
- 不支持 `Transform2d` 节点。
