# NRDHelpers.slang 源码文档

> 路径: `Source/Falcor/RenderPasses/Shared/Denoising/NRDHelpers.slang`
> 类型: Slang 着色器模块
> 模块: 渲染通道/共享/降噪 (RenderPasses/Shared/Denoising)

## 功能概述

本文件提供 NRD 降噪器的辅助函数，主要用于计算材质反射率以支持 Delta 路径（完美镜面反射/透射）的降噪处理。包含根据材质类型（标准材质、头发材质）计算适当反射率的逻辑，以及判断 Delta 透射路径上是否允许 Delta 反射的条件检查。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 getMaterialReflectanceForDeltaPaths(MaterialType, bool hasDeltaLobes, ShadingData sd, BSDFProperties bsdfProperties)` | 根据材质类型计算 Delta 路径的反射率。标准材质区分金属/非金属：非金属返回漫反射+漫透射反照率之和；金属且无 Delta 叶时使用 GGX 近似镜面积分；头发材质返回反射+透射反照率之和 |
| `bool isDeltaReflectionAllowedAlongDeltaTransmissionPath(ShadingData sd)` | 判断在 Delta 透射路径上是否允许 Delta 反射。条件：全内反射（TIR）或非透射镜面（`specularTransmission == 0` 且 `metallic == 1`） |

## 实现细节

- `getMaterialReflectanceForDeltaPaths`:
  - 对于标准材质 (`MaterialType::Standard`)，从 `BasicMaterialData` 中读取金属度（`specular.b`），分三种情况：
    1. 非金属（`metallic < 1`）：使用漫反射和漫透射反照率之和
    2. 金属且非 Delta 叶：使用 `approxSpecularIntegralGGX` 近似镜面反射积分
    3. 其他情况：返回 1.0
  - 对于头发材质 (`MaterialType::Hair`)：返回漫反射和漫透射反照率之和
  - 所有反射率下限为 `kNRDMinReflectance`（0.01）

- `isDeltaReflectionAllowedAlongDeltaTransmissionPath`:
  - 计算折射率比 `eta`，通过 `evalFresnelDielectric` 判断是否全内反射
  - 检查是否为非透射的纯镜面材质

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Rendering.Materials.Fresnel` | 菲涅尔计算函数 |
| `Rendering.Materials.IMaterialInstance` | 材质实例接口 |
| `Rendering.Materials.IsotropicGGX` | 各向同性 GGX 近似镜面积分 |
| `RenderPasses.Shared.Denoising.NRDConstants` | NRD 常量 |
| `Scene.Scene` | 场景模块（材质数据访问） |
| `Scene.ShadingData` | 着色数据结构 |
