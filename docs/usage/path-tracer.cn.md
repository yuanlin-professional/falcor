### [Index](../index.md) | [Usage](./index.md) | Path Tracer

--------

# Path Tracer

## 快速开始

1. 构建 Falcor
2. 启动 Mogwai
3. 示例 Path Tracer 渲染图文件位于 `Source/Mogwai/Data/PathTracer.py`。还有一个替代的实时降噪 Path Tracer 渲染图位于 `Source/Mogwai/Data/PathTracerNRD.py`。您可以使用以下任一方法加载它们：
    - 按 `Ctrl + O`（或点击顶部菜单栏中的 `Load Script`），然后导航到 `PathTracer.py` 或 `PathTracerNRD.py`。
    - 将 `PathTracer.py` 或 `PathTracerNRD.py` 拖放到应用程序窗口中。
    - 使用 Mogwai 的 `--script` 命令行选项在启动时加载脚本。
4. 使用以下方法之一加载模型或场景。包含一个示例场景，位于 `media/Arcade/Arcade.pyscene`。Falcor 还可以加载 Assimp 支持的任何格式，以及 USD 和 pbrt-v4 场景。
    - 按 `Ctrl + Shift + O`（或点击顶部菜单栏中的 `Load Scene`），然后选择文件。
    - 将文件拖放到应用程序窗口中。
    - 使用 Mogwai 的 `--scene` 命令行选项在启动时加载场景。

## 概述

![PathTracer-overview](./images/path-tracer-overview.png)

`PathTracer` render pass 在 DXR 1.1 中实现了一个无偏路径追踪器。路径在全屏计算通道中生成。一个 raygen shader 然后为每个像素循环遍历路径段，直到达到配置的最大路径长度。着色在 hit/miss shader 中完成。可见性光线使用光线查询（`TraceRayInline`）进行追踪。

对于屏幕上的每个像素，追踪 `samplesPerPixel` 条路径。通常设置为 `1` 或较小的数值。渐进式渲染通过将低采样率图像馈送到后续的 `AccumulatePass` render pass 来实现。

在每个路径顶点（黑点），向采样的光源追踪一条可见性光线（虚线）。光源的采样方式是首先从最多三种光源采样策略中随机选择一种（I：解析光源，II：环境贴图，III：网格光源），然后使用所选策略进行重要性采样。对于（II）和（III），使用多重重要性采样（MIS）。

注意：直接照明也可以通过启用 RTXDI 使用 ReSTIR 进行采样。

弹跳次数通过 `maxSurfaceBounces`、`maxDiffuseBounces`、`maxSpecularBounces` 和 `maxTransmissionBounces` 选项配置。如果设置为 `0`，则只计算直接照明。如果设置为 `1`，则计算 1 次弹跳的间接照明。通常 `maxTransmissionBounces` 设置为较高值（例如 `10`），以允许路径穿过多个介电界面。`maxDiffuseBounces` 和 `maxSpecularBounces` 值通常设置较低（例如 `3`）以提高性能。`maxSurfaceBounces` 自动确定为其他三个值的最大值。

请注意，在最后一个路径顶点，如果场景有自发光体和/或启用了 MIS，会使用 BSDF 采样追踪最后一条光线，以避免遗漏最后一个路径顶点的任何直接照明贡献。

### 嵌套介电材质

可以在 `.pyscene` 文件中将材质配置为透射材质（玻璃、液体等）。要渲染重叠的介电材质（例如玻璃中的液体），网格应该重叠以避免边界处的数值问题和空气间隙。同时重要的是将透射材质设为双面。

Falcor 使用基于栈的方法来解析这些*嵌套介电*材质之间的优先级。具有较高 `nestedPriority` 属性的材质具有更高的优先级。

**注意：** 默认值 `nestedPriority = 0` 保留为最高优先级。

在 .pyscene 文件中配置材质的示例（有关 Python API 的详细信息，请参阅[脚本](scripting.md)）：

```python
# 吸收系数（或在无散射时为消光系数）
# 来自 https://cseweb.ucsd.edu/~ravir/dilution.pdf，并为 Falcor 场景单位（米）重新缩放
volume_absorption = {
    'white_wine': float3(12.28758, 16.51818, 20.30273),
    'red_wine': float3(117.13133, 251.91133, 294.33867),
    'beer': float3(11.78552, 25.45862, 58.37241),
    'bottle_wine': float3(102.68063, 168.015, 246.80438)
}

# 为嵌套介电体配置场景的现有材质。
glass = sceneBuilder.getMaterial("TransparentGlass")
glass.roughness = 0
glass.metallic = 0
glass.indexOfRefraction = 1.55
glass.specularTransmission = 1
glass.doubleSided = True
glass.nestedPriority = 5

bottle_wine = sceneBuilder.getMaterial("TransparentGlassWine")
bottle_wine.roughness = 0
bottle_wine.metallic = 0
bottle_wine.indexOfRefraction = 1.55
bottle_wine.specularTransmission = 1
bottle_wine.doubleSided = True
bottle_wine.nestedPriority = 5
bottle_wine.volumeAbsorption = volume_absorption['bottle_wine']

water = sceneBuilder.getMaterial("Water")
water.roughness = 0
water.metallic = 0
water.indexOfRefraction = 1.33
water.specularTransmission = 1
water.doubleSided = True
water.nestedPriority = 1

ice = sceneBuilder.getMaterial("Ice")
ice.roughness = 0.1
ice.metallic = 0
ice.indexOfRefraction = 1.31
ice.specularTransmission = 1
ice.doubleSided = True
ice.nestedPriority = 4
```

### Render Pass 输入

Path tracer 仅需要一个轻量级的 V-buffer 作为输入。V-buffer 仅编码命中的网格/图元索引和重心坐标。基于这些属性，path tracer 在主要命中点获取顶点和材质数据。

还有一些可选输入，仅在启用某些功能时使用。以下是所有输入的列表：

| 名称 | 可选 | 描述 |
| --- | --- | --- |
| `vbuffer` | 否 | 编码主要命中点。 |
| `mvec` | 是 | 屏幕空间运动向量（在启用 RTXDI 进行直接照明采样时需要，否则时间重采样将无法正常工作）。 |
| `viewW` | 是 | 世界空间视线方向（需要正确渲染景深效果，否则视线方向指向相机原点而非实际透镜采样位置）。 |
| `sampleCount` | 是 | 每像素采样数（用于自适应采样）。 |

### Render Pass 输出

Path tracer render pass 的所有输出都是可选的，仅在连接到后续 render pass 时才会计算。

| 名称 | 格式 | 描述 |
| --- | --- | --- |
| `color` | `RGBA32Float` | 每个像素的最终辐射度。 |
| `albedo` | `RGBA8Unorm`  | 主要命中点的漫反射反照率。 |
| `specularAlbedo` | `RGBA8Unorm`  | 主要命中点的镜面反照率。 |
| `indirectAlbedo` | `RGBA8Unorm`  | 次级命中点的漫反射反照率（或自发光）。 |
| `normal` | `RGBA16Float` | 主要命中点的世界空间法线。 |
| `reflectionPosW` | `RGBA32Float` | 次级命中点的世界空间位置。 |
| `rayCount` | `R32Uint` | 每像素追踪的光线数量，用于调试/可视化。 |
| `pathLength` | `R32Uint` | 每像素的路径长度，用于调试/可视化。 |
| `nrd*` | - | 许多用于 NRD 降噪器的附加输出。 |

## 采样策略

注意：多重重要性采样应用于标记为 MIS 的策略。

### BSDF 采样 (MIS)

- `StandardMaterial`：
  - Disney 各向同性漫反射。
  - Trowbridge-Reitz GGX 镜面反射/透射，使用 VNDF 采样。
  - 漫反射/镜面反射或透射通过随机方式选择。
  - 当材质粗糙度接近零时使用理想镜面反射/透射。

### 环境贴图采样 (MIS)

- 在启动时计算分层重要性贴图（mipmap）。
- 通过对 2D 均匀数的分层变换进行重要性采样。
- PDF 与入射辐射度成正比，忽略余弦项和可见性。

### 自发光网格光源采样 (MIS)

- 在所有自发光三角形上构建光源 BVH。
- 预积分每个三角形的光通量，并剔除零发光的三角形。
- 分层重要性采样（参见《Ray Tracing Gems》一书第 18 章）。
- 提供可选的均匀采样和等功率光源采样模式。

### 解析光源采样

- 用于点光源、方向光、远距离光以及四边形/圆盘/球体面光源。
- 光源在 FBX 文件（点光源、方向光）或 Python 场景文件（.pyscene）中指定。
- 每个光源以相等概率被选中。


## 验证/调试工具

### MinimalPathTracer

- 独立的 `MinimalPathTracer` 通道。
- 朴素/简单设计，便于验证，无 MIS 等。
- 生成真值图像（但收敛较慢）。
- 不支持嵌套介电材质。

### ErrorMeasurePass

- 接受源图像和参考图像。
- 参考图像可以从磁盘加载，或从通道输入获取。
- 使并行运行两个不同配置并比较其输出成为可能。

### Shader print/assert

- `PathTracer` 通道支持在 shader 中使用 `print()` 进行调试。
- 在 UI 中点击 *Pixel Debug* 启用，点击一个像素查看其输出。
- 冻结随机种子（UI 选项）可避免数值闪烁，非常实用。
- 还有一个 `assert()` 调用，用于打印触发断言的坐标。
- 调试消息同时显示在 UI 和控制台中。
