# RGLMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/RGLMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

RGL 实测材质的 BSDF 实例。使用 NDF、VNDF 和亮度分布进行重要性采样，通过 RGB 查找表获取反射率。

## 结构体与接口

### `RGLMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `slice` | `float2` | 预计算的入射角切片坐标 |
| `sigma` | `float` | 预计算的微表面投影面积 |
| `ndfVndfSize` | `uint2` | 压缩的 NDF/VNDF 尺寸 |
| `lumiSize` | `uint2` | 压缩的亮度 LUT 尺寸 |

#### 采样策略

使用级联采样：先采样亮度分布，再采样 VNDF，最终通过雅可比变换得到出射方向。

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Scene.Material.RGLMaterialData`
- `Rendering.Materials.RGLCommon`
