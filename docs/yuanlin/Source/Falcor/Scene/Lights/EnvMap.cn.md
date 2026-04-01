# EnvMap 源码文档

> 路径: `Source/Falcor/Scene/Lights/EnvMap.h`, `Source/Falcor/Scene/Lights/EnvMap.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Lights

## 功能概述

环境贴图（Environment Map）类，用于存储和评估以经纬度映射（lat-long）形式的环境辐照度纹理。支持旋转、强度缩放、色调调节和着色器数据绑定。是场景中基于图像的照明（IBL）的核心组件。

## 类与接口

### `EnvMap`

- **继承**: `Object`
- **职责**: 管理环境贴图纹理及其参数，提供 GPU 端的数据绑定。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<EnvMap> create(ref<Device>, const ref<Texture>&)` | 从纹理创建环境贴图 |
| `static ref<EnvMap> createFromFile(ref<Device>, const path&)` | 从文件加载环境贴图 |
| `void setRotation(float3 degreesXYZ)` | 设置旋转角度（度，绕 Z/Y/X 轴） |
| `void setTransform(const float4x4& xform)` | 从变换矩阵提取旋转并设置 |
| `void setIntensity(float intensity)` | 设置强度缩放因子 |
| `void setTint(const float3& tint)` | 设置色调（RGB 乘法因子） |
| `void bindShaderData(const ShaderVar& var) const` | 绑定环境贴图数据到着色器 |
| `Changes beginFrame()` | 检测帧间变化（变换、强度） |
| `uint64_t getMemoryUsageInBytes() const` | 获取 GPU 内存使用量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpEnvMap` | `ref<Texture>` | 环境贴图纹理（RGB） |
| `mpEnvSampler` | `ref<Sampler>` | 纹理采样器（水平环绕、垂直钳位） |
| `mData` | `EnvMapData` | 环境贴图数据（含变换矩阵、强度、色调） |
| `mRotation` | `float3` | 当前旋转角度 |

## 依赖关系

### 本文件引用

- `EnvMapData.slang` — 环境贴图数据结构
- `Core/API/Texture.h`, `Core/API/Sampler.h` — 纹理和采样器
- `Utils/Scripting/ScriptBindings.h` — Python 脚本绑定

### 被以下文件引用

- `Scene/Scene.h` — 场景管理
- 着色器端的 `EnvMap.slang`
- 各种光线追踪渲染通道

## 实现细节

- 采样器配置为水平方向环绕（Wrap）、垂直方向钳位（Clamp），适合经纬度映射。
- 加载纹理时启用 mipmap 生成，使用线性颜色空间。
- 变换矩阵在设置旋转时同时计算正向和逆向变换。
- 通过 `FALCOR_SCRIPT_BINDING` 导出到 Python，支持文件加载、旋转/强度/色调属性设置。
