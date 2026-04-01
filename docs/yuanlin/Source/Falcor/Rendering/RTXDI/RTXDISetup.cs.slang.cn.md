# RTXDISetup.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/RTXDISetup.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/RTXDI

## 功能概述

本文件定义了 RTXDI 主要采样/重采样操作的计算着色器入口点。每个入口点对应 RTXDI 重采样管线中的一个阶段，由 C++ 端的 `RTXDI::update()` 按顺序调度执行。

## 函数

| 入口点 | 线程组 | 说明 |
|--------|--------|------|
| `presampleLocalLights` | [256, 1, 1] | 将局部光源预采样到瓦片中，提升内存访问一致性 |
| `presampleEnvLight` | [256, 1, 1] | 将环境光预采样到瓦片中 |
| `generateCandidates` | [16, 16, 1] | 为每个像素生成候选光源样本 |
| `testCandidateVisibility` | [16, 16, 1] | 测试单个选中候选光源的可见性 |
| `spatialResampling` | [16, 16, 1] | 仅空间重采样 |
| `temporalResampling` | [16, 16, 1] | 仅时间重采样（回溯上一帧反投影样本） |
| `spatiotemporalResampling` | [16, 16, 1] | 时空联合重采样 |

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gOutputReservoirID` | `uint` | 输出蓄水池 ID |
| `gInputReservoirID` | `uint` | 输入蓄水池 ID（初始候选或上一轮空间重采样结果） |
| `gTemporalReservoirID` | `uint` | 时间蓄水池 ID（上一帧采样结果） |

## 依赖关系 / import

- `Rendering.RTXDI.RTXDI`（通过 `gRTXDI` 全局变量调用各操作方法）

## 实现细节

- 预采样入口点使用 256 线程的一维线程组，其中 `threadID.x` 为瓦片内样本索引，`threadID.y` 为瓦片索引
- 重采样入口点使用 16x16 的二维线程组，与 RTXDI SDK 的沸腾滤波器要求一致
- 时间和时空重采样的入口点额外传入 `groupID`（组内线程 ID），供沸腾滤波器使用
