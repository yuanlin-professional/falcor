# DepthPass.3d.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/DepthPass.3d.slang`
> 类型: Slang 3D 光栅化着色器
> 模块: RenderPasses/GBuffer

## 功能概述

G-buffer 光栅化渲染的深度预通道着色器。仅执行顶点变换和可选的 Alpha 测试，用于填充深度缓冲区。后续的 G-buffer 通道使用等深度测试来避免不必要的像素着色。

## 函数

| 函数 | 说明 |
|------|------|
| `vsMain(VSIn)` | 顶点着色器，执行默认顶点变换 |
| `psMain(VSOut, uint)` | 像素着色器，仅执行可选的 Alpha 测试（丢弃不通过的片元） |

## 依赖关系 / import

- `Scene.Raster` — 光栅化场景接口
