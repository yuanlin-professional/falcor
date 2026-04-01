# SampleGeneratorType.slangh 源码文档

> 路径: `Source/Falcor/Utils/Sampling/SampleGeneratorType.slangh`
> 类型: Slang 头文件（.slangh）
> 模块: Utils/Sampling

## 功能概述

定义了采样生成器的 **类型常量**，作为 CPU/GPU 共享的宏定义，用于在主机端和着色器端统一标识不同类型的采样器。此文件同时被 C++ 代码和 Slang 着色器代码包含。

## 结构体与接口

无结构体或接口定义。

## 函数

无函数定义。

## 宏定义

| 宏名称 | 值 | 说明 |
|--------|-----|------|
| `SAMPLE_GENERATOR_TINY_UNIFORM` | `0` | 轻量级 32 位均匀随机采样器（LCG 实现） |
| `SAMPLE_GENERATOR_UNIFORM` | `1` | 标准 128 位均匀随机采样器（Xoshiro128** 实现） |
| `SAMPLE_GENERATOR_DEFAULT` | `SAMPLE_GENERATOR_UNIFORM` | 默认采样器类型，指向标准均匀采样器 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享类型定义头文件

## 实现细节

- 使用 `#pragma once` 防止重复包含
- 此文件使用 `.slangh` 扩展名，表示可以被 Slang 和 C++ 代码共同包含
- 类型常量被 `SampleGenerator.h`（C++ 端）和 `SampleGenerator.slang`（GPU 端）同时引用，确保类型标识一致
