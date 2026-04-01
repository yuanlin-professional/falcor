# INDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/INDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

定义法线分布函数（NDF）接口 `INDF`，基于微表面理论中的"拉伸不变"假设。仅需实现标准配置（各向同性、单位粗糙度 alpha=1.0）下的斜率分布即可推导出各向异性变体和精确的 Smith 遮蔽函数。参考 Eric Heitz 2014 的论文。

## 结构体与接口

### `INDF`（接口）

| 方法签名 | 说明 |
|----------|------|
| `evalP22Std(float2 slope)` | 求值标准配置下的斜率分布 P22_std |
| `evalSigmaStd(float3 dir)` | 求值标准配置下的投影面积 sigma_std |
| `sampleP22Std(sg)` | 按 P22_std 比例采样斜率 |
| `sampleVisibleP22Std(wi, sg)` | 按可见 P22_std 比例采样斜率 |

## 依赖关系

### export
- `Utils.Sampling.SampleGeneratorInterface`
