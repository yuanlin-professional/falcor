# HelloDXR.3d.slang 着色器文档

> 路径: `Source/Samples/HelloDXR/HelloDXR.3d.slang`
> 类型: Slang 着色器
> 模块: Samples/HelloDXR

## 功能概述

HelloDXR 示例的光栅化渲染着色器,包含顶点着色器和像素着色器。顶点着色器执行标准的场景光栅化变换,像素着色器计算直接光照(来自解析光源),支持 Alpha 测试和材质系统的 BSDF 求值。

## 结构体与接口

本着色器使用 Falcor 内置的 `VSIn`/`VSOut` 结构体(通过 `Scene.Raster` 导入)。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `VSOut vsMain(VSIn vIn)` | 顶点着色器入口,调用 `defaultVS` 执行标准顶点变换 |
| `float4 psMain(VSOut vsOut, uint triangleIndex: SV_PrimitiveID) : SV_TARGET` | 像素着色器入口,执行 Alpha 测试、准备着色数据、计算材质自发光和所有解析光源的直接光照 |

## 实现细节

- 使用 `ImplicitLodTextureSampler` 进行隐式 LOD 纹理采样。
- 通过 `alphaTest` 进行透明度裁剪。
- 使用 `prepareShadingData` 从光栅化输出构建着色数据。
- 通过 `gScene.materials.getMaterialInstance` 获取材质实例,使用 BSDF 评估光照。
- 使用 `TinyUniformSampleGenerator` 作为随机采样器(基于像素坐标)。
- 遍历所有解析光源调用 `evalLightApproximate` 计算近似光照,再通过材质实例的 `eval` 方法得到最终颜色。

## 依赖关系 / import

| 模块 | 说明 |
|------|------|
| `Scene.Raster` | 场景光栅化支持(顶点变换、着色数据准备、Alpha 测试) |
| `Utils.Sampling.TinyUniformSampleGenerator` | 轻量级均匀采样生成器 |
| `Rendering.Lights.LightHelpers` | 解析光源辅助函数(`evalLightApproximate`) |
