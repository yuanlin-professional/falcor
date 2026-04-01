# HomogeneousVolumeSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Volumes/HomogeneousVolumeSampler.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/Volumes

## 功能概述

本文件实现了均匀介质（Homogeneous Volume）的采样器。均匀介质的吸收系数和散射系数在整个体积内恒定，因此可以使用解析公式进行透射率计算和散射距离采样。采用"英雄波长"（Hero Wavelength）策略选择 RGB 通道进行距离采样。

## 结构体与接口

### `HomogeneousVolumeSampler`

静态方法集合，无实例状态。

### `HomogeneousVolumeSampler::DistanceSample`

散射距离采样结果。

| 成员 | 类型 | 说明 |
|------|------|------|
| `t` | `float` | 散射距离 |
| `sigmaS` | `float3` | 散射系数 |
| `sigmaT` | `float3` | 消光系数（sigmaA + sigmaS） |
| `channelProbs` | `float3` | RGB 通道选择概率 |

## 函数

| 函数 | 说明 |
|------|------|
| `DistanceSample::evalThroughput(hasScattered, surfaceT) -> float3` | 计算路径吞吐量（散射或透射情况） |
| `static sampleDistance<S>(sigmaA, sigmaS, thp, sg, out ds) -> bool` | 从吸收/散射系数采样散射距离 |
| `static sampleDistance<S>(vp, thp, sg, out ds) -> bool` | 从 `VolumeProperties` 采样散射距离 |
| `static evalTransmittance(sigmaT, distance) -> float3` | 评估均匀介质透射率 `exp(-distance * sigmaT)` |
| `static evalTransmittance(vp, distance) -> float3` | 从 `VolumeProperties` 评估透射率 |

## 依赖关系 / import

- `Scene.Material.VolumeProperties`（`__exported import`，导出 `VolumeProperties`）
- `Utils.Sampling.SampleGeneratorInterface`（`__exported import`）

## 实现细节

- **通道选择概率**: 基于当前路径吞吐量和反照率 `channelProbs = thp * albedo`，归一化后用于选择 RGB 通道
- **距离采样**: 使用选定通道的散射系数进行逆 CDF 采样：`t = -log(1 - u) / sigmaS[channel]`
- **吞吐量评估**:
  - 散射情况: `Tr * sigmaS / pdf`（其中 `pdf = dot(channelProbs, Trs * sigmaS)`）
  - 透射情况: `Tr / pdf`（其中 `pdf = dot(channelProbs, Trs)`）
  - `Trs` 使用散射系数而非消光系数，以避免除零问题
- 当所有通道的散射系数都为零时直接返回 false（介质不散射）
- 当通道概率和过小时，回退为均匀三通道概率（1/3）
