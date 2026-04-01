# ShadingUtils.slang 着色器文档
> 路径: `Source/Falcor/Scene/Material/ShadingUtils.slang`
> 类型: Slang 着色器
> 模块: Scene/Material

## 功能概述
提供着色相关的工具函数，包括法线贴图应用、着色法线翻转和调整、半球检查等。这些工具函数被各种材质实现广泛使用。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 rgbToNormal(float3 rgb)` | 将 RGB 编码转换为法线向量 |
| `float3 rgToNormal(float2 rg)` | 将 RG 编码转换为法线向量 |
| `float getMetallic(float3 diffuse, float3 spec)` | 从漫反射和高光颜色估算金属度（可微） |
| `ShadingFrame computeShadingFrameFromNormalMap(ShadingData, NormalMapType, float3)` | 从法线贴图计算着色帧 |
| `void flipShadingNormal(ShadingData, inout ShadingFrame)` | 双面材质背面翻转着色法线 |
| `void adjustShadingNormal(ShadingData, inout ShadingFrame)` | 调整着色法线以减少掠射角的黑色像素 |
| `bool isValidHemisphereReflection(...)` | 反射方向的半球合法性检查 |
| `bool isValidHemisphereTransmission(...)` | 透射方向的半球合法性检查 |
| `bool isValidHemisphereReflectionOrTransmission(...)` | 反射或透射方向的半球合法性检查 |

## 依赖关系
### import
- `Rendering.Materials.IBSDF` - 双向散射分布函数接口
- `Scene.ShadingData` - 着色数据
- `Scene.Material.MaterialTypes` - 材质类型
- `Utils.Color.ColorHelpers` - 颜色辅助函数
- `Utils.Math.ShadingFrame` - 着色帧

## 实现细节
- `FALCOR_BACKFACE_BLACK` 宏启用时，背面几何体强制为黑色
- `adjustShadingNormal` 在掠射角（cosTheta < 0.1）时将着色法线向几何法线混合，避免视线方向变为背面
- 半球检查同时验证局部空间中的方向和世界空间中的几何面朝向一致性
