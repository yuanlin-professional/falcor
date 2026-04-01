# Shading.slang 源码文档

> 路径: `Source/Falcor/Scene/Shading.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

Shading.slang 是着色所需依赖的聚合模块。它将场景数据（`Scene`）、着色数据（`ShadingData`）、纹理采样器（`TextureSampler`）和材质工厂（`MaterialFactory`）统一导出，使得需要执行着色计算的着色器只需导入此单一模块即可获得所有必要依赖。

如果着色器只需要访问场景数据而不需要着色功能，可以直接导入 `Scene.Scene`。

## 函数

无自定义函数。此文件仅作为导出聚合。

## 依赖关系 / import

- `Scene.Scene`（导出） - 场景数据结构和全局 `gScene`
- `Scene.ShadingData`（导出） - 着色数据结构
- `Scene.Material.TextureSampler`（导出） - 纹理采样器接口
- `Scene.Material.MaterialFactory`（导出） - 材质实例工厂
