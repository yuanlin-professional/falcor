# EnvMapConverter 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/EnvMapConverter.h`
> 类型: C++ 头文件（仅头文件）
> 模块: plugins/importers/PBRTImporter

## 功能概述

GPU 辅助类，将环境贴图从等面积八面体映射（equal-area octahedral mapping）转换为经纬度映射（lat-long mapping）。pbrt-v4 使用八面体映射格式，而 Falcor 使用经纬度格式。

## 类与接口

### `EnvMapConverter`

- **职责**: 编译并执行计算着色器以转换环境贴图格式。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `EnvMapConverter(ref<Device> pDevice)` | 构造函数，编译着色器并创建线性钳位采样器 |
| `ref<Texture> convertEqualAreaOctToLatLong(RenderContext*, const ref<Texture>&)` | 执行转换。输入必须为正方形纹理。输出尺寸为 [2*width, height]，格式 RGBA32Float |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mpComputePass` | `ref<ComputePass>` | 计算着色器通道 |
| `mpSampler` | `ref<Sampler>` | 线性钳位采样器 |

## 依赖关系

### 本文件引用

- `Core/API/Formats.h`、`Sampler.h`、`RenderContext.h` - GPU API
- `Core/Pass/ComputePass.h` - 计算通道
- `EnvMapConverter.cs.slang` - 计算着色器

### 被以下文件引用

- `PBRTImporter.cpp` - 导入 infinite 灯光时使用
