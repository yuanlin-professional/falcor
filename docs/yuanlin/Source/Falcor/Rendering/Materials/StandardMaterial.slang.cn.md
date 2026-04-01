# StandardMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/StandardMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

Falcor 标准表面材质的实现。支持 MetalRough 和 SpecGloss 两种着色模型。材质是六种 BSDF 叶瓣的线性组合：Delta/镜面/漫反射 的反射和透射。默认单面，支持双面镜像。

## 结构体与接口

### `StandardMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `StandardMaterialInstance`

#### 关键功能
- 采样基础颜色、镜面纹理、法线贴图、发射纹理
- MetalRough: 粗糙度=G通道、金属度=B通道
- SpecGloss: 镜面颜色=RGB、光泽度=A通道
- 可微分版本支持材质梯度 IO

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.StandardMaterialInstance`（导出）
- `Scene.Material.StandardMaterialParamLayout`
- `Scene.Material.BasicMaterialData`
- `Scene.Material.ShadingUtils`
