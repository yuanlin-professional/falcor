# EnvMapSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EnvMapSampler.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件实现了环境贴图采样和评估的 GPU 端逻辑。使用主机端 `EnvMapSampler` 类构建的分层重要性贴图（Hierarchical Importance Map）进行重要性采样。采样算法自顶向下遍历 mipmap 金字塔，在每一层通过 2x2 子区域的权重进行水平和垂直方向的二维 warp 变换，最终得到一个与环境贴图亮度成比例的采样方向。

## 结构体与接口

### `EnvMapSample`

- **职责**: 存储环境贴图采样结果

| 成员 | 类型 | 说明 |
|------|------|------|
| `dir` | `float3` | 采样方向（世界空间） |
| `pdf` | `float` | 相对于立体角的概率密度 |
| `Le` | `float3` | 发射辐射度 |

### `EnvMapSampler`

- **职责**: GPU 端环境贴图重要性采样与评估

| 成员 | 类型 | 说明 |
|------|------|------|
| `importanceSampler` | `SamplerState` | 点采样+边缘夹取 |
| `importanceMap` | `Texture2D<float>` | 分层重要性贴图（完整 mipmap 链） |
| `importanceInvDim` | `float2` | 1.0 / 维度 |
| `importanceBaseMip` | `uint` | 1x1 分辨率的 mip 级别 |

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 eval(float3 dir, float lod = 0.f)` | 评估指定世界空间方向的环境辐射度 |
| `bool sample(const float2 rnd, out EnvMapSample result, float lod = 0.f)` | 基于重要性贴图进行采样，返回方向、PDF 和辐射度 |
| `float evalPdf(float3 dir)` | 评估指定方向的概率密度（用于 MIS） |

## 实现细节

### `sample`

1. 从 1x1 mip（`importanceBaseMip - 1`）开始，逐层向下遍历到最精细层
2. 每层加载 2x2 纹素权重，先沿水平方向 warp（左/右子区域），再沿垂直方向 warp（上/下子区域）
3. 随机数 `p` 在每次 warp 后被重新缩放到 [0,1)，保持均匀性
4. 最终得到纹素坐标 `pos` 和子纹素位置 `p`
5. 将 UV 坐标转换为等面积八面体映射方向（`oct_to_ndir_equal_area_unorm`）
6. PDF = 采样点亮度 / 平均亮度 / 4pi

### `evalPdf`

- 将方向转换为八面体 UV，查询重要性贴图值，除以平均值再除以 4pi

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Scene` — 场景全局变量（环境贴图访问）
- `Utils.Math.MathHelpers` — 八面体映射等数学辅助函数
