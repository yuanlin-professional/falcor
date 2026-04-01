# ResolvePass.cs.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/ResolvePass.cs.slang`
> 类型: Slang 计算着色器
> 模块: RenderPasses/PathTracer

## 功能概述

实现路径追踪器的样本解析（Resolve）通道。从基于 Tile 的样本缓冲区中读取路径追踪器生成的每像素多样本数据，通过平均计算最终像素值。同时解析降噪引导数据和 NRD 数据，包括漫反射/镜面/Delta 反射/Delta 透射/残余辐射度的分离和解调。

## 结构体与接口

### `ResolvePass`

| 成员 | 说明 |
|------|------|
| `params` | 运行时路径追踪参数 |
| `sampleColor` | 输入每样本颜色 |
| `sampleGuideData` | 输入每样本引导数据 |
| `sampleNRDRadiance` / `sampleNRDHitDist` | 输入 NRD 辐射度和命中距离 |
| `outputColor` | 输出解析后颜色 |
| `outputAlbedo` / `outputGuideNormal` 等 | 输出解析后引导数据 |
| `outputNRDDiffuseRadianceHitDist` 等 | 输出 NRD 各通道解析结果 |

## 函数

| 函数 | 说明 |
|------|------|
| `execute(uint2 pixel)` | 主入口，解析指定像素的所有样本并写入输出。对颜色求平均，对 NRD 数据进行解调和分离 |

## 依赖关系 / import

- `RenderPasses.Shared.Denoising.NRDData` — NRD 数据类型
- `ColorType`, `GuideData`, `Params` — 本模块类型
