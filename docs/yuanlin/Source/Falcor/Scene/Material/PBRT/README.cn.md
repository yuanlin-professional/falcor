# PBRT Material -- PBRT 兼容材质

> 源码路径: `Source/Falcor/Scene/Material/PBRT/`

## 功能概述

PBRT 子目录包含与 PBRT（Physically Based Rendering Toolkit）渲染器完全兼容的材质类型实现。这些材质类型从 `BasicMaterial` 派生，遵循 PBRT 的材质定义标准，使得 Falcor 能够准确地加载和渲染 PBRT 格式的场景文件。

该模块提供了六种 PBRT 材质类型：`PBRTDiffuseMaterial`（朗伯漫反射材质）、`PBRTConductorMaterial`（导体材质，基于复数折射率的菲涅尔方程）、`PBRTDielectricMaterial`（电介质材质，同时具有反射和折射）、`PBRTCoatedConductorMaterial`（涂层导体材质，电介质涂层+导体基底）、`PBRTCoatedDiffuseMaterial`（涂层漫反射材质，电介质涂层+漫反射基底）和 `PBRTDiffuseTransmissionMaterial`（漫反射透射材质，具有反射和透射两个瓣）。

所有 PBRT 材质共享 `BasicMaterial` 的纹理通道基础设施，但各自定义了不同的通道语义。导体和涂层材质支持各向异性 GGX 微面模型，涂层材质还通过蒙特卡洛随机游走模拟层间互反射。部分材质类型（如 `PBRTDiffuseMaterial` 和 `PBRTConductorMaterial`）还实现了可微分参数布局接口，支持参数序列化和反序列化。

## 文件清单

| 文件 | 类型 | 说明 |
|------|------|------|
| `PBRTDiffuseMaterial.h/.cpp` | C++ | 朗伯漫反射材质，反射率不依赖出射方向 |
| `PBRTDiffuseMaterialParamLayout.slang` | Slang | PBRTDiffuseMaterial 的可微分参数布局 |
| `PBRTConductorMaterial.h/.cpp` | C++ | 导体材质，纯反射表面，支持各向异性 GGX 粗糙度 |
| `PBRTConductorMaterialParamLayout.slang` | Slang | PBRTConductorMaterial 的可微分参数布局 |
| `PBRTDielectricMaterial.h/.cpp` | C++ | 电介质材质，具有反射和折射，支持各向异性粗糙度 |
| `PBRTCoatedConductorMaterial.h/.cpp` | C++ | 涂层导体材质，电介质涂层覆盖导体基底，蒙特卡洛层间互反射 |
| `PBRTCoatedDiffuseMaterial.h/.cpp` | C++ | 涂层漫反射材质，电介质涂层覆盖朗伯基底 |
| `PBRTDiffuseTransmissionMaterial.h/.cpp` | C++ | 漫反射透射材质，同时具有反射和透射瓣 |

## 依赖关系

- **上游依赖**: `Scene/Material/BasicMaterial`, `Scene/Material/Material`, `Scene/Material/MaterialParamLayout`, `Scene/Material/SerializedMaterialParams`
- **下游被依赖**: `Scene/Material/MaterialSystem`（通过类型注册机制管理）, PBRT 场景导入器

## 关键类与接口

### `PBRTDiffuseMaterial`
朗伯漫反射材质。纹理通道：BaseColor (RGB=基色, A=不透明度)，Normal。支持可微分参数布局。

### `PBRTConductorMaterial`
导体材质，表面为纯反射。使用复数折射率（eta + i*k）控制菲涅尔反射。纹理通道：BaseColor (RGB=复数 Eta)，Transmission (RGB=复数 k)，Specular (R=X粗糙度, G=Y粗糙度)，Normal。粗糙度为零时表现为完美镜面，否则使用各向异性 GGX 微面分布。支持可微分参数布局。

### `PBRTDielectricMaterial`
电介质材质，同时具有无色反射和折射瓣。纹理通道：Specular (R=X粗糙度, G=Y粗糙度)，Normal。粗糙度为零时为理想光滑表面。

### `PBRTCoatedConductorMaterial`
涂层导体材质，模拟电介质涂层覆盖导体基底的双层结构。涂层和基底均可独立设置各向异性粗糙度。纹理通道：BaseColor (RGB=导体 Eta)，Transmission (RGB=导体 k)，Specular (R=涂层X粗糙度, G=涂层Y粗糙度, B=导体X粗糙度, A=导体Y粗糙度)。

### `PBRTCoatedDiffuseMaterial`
涂层漫反射材质，电介质涂层覆盖朗伯基底。纹理通道：BaseColor (RGB=漫反射反照率)，Specular (R=涂层X粗糙度, G=涂层Y粗糙度)。

### `PBRTDiffuseTransmissionMaterial`
漫反射透射材质，反射瓣和透射瓣均为朗伯型。纹理通道：BaseColor (RGB=反射基色)，Transmission (RGB=透射颜色)。
