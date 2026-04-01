# NormalizedDenseSDFGrid - 归一化密集SDF网格

## 功能概述

本目录实现了归一化密集有符号距离场（Normalized Dense SDF Grid，简称 NDSDFGrid），这是 Falcor 框架中 SDF 网格的一种表示方式。该实现将 SDF 数据存储为一组多级 3D 纹理（LOD），使用归一化的 SNorm8 格式，距离值根据"窄带厚度"（narrow band thickness）参数进行归一化。

核心特性：

- **多级 LOD 结构**：从粗糙级别到精细级别的多层纹理表示，最粗级别宽度不小于 8，每一级分辨率翻倍
- **归一化距离**：距离值被归一化为 `[-1, 1]` 范围的 SNorm8 格式，归一化因子与网格宽度和窄带厚度相关
- **GPU 光线追踪求交**：Slang 着色器实现了自适应 LOD 的球追踪（sphere tracing）算法，根据采样距离值动态切换 LOD 级别
- **双采样模式**：硬件采样（hwSample，使用纹理硬件三线性插值，速度快但精度低）和软件采样（swSample，手动三线性插值，精度高）
- **梯度计算**：支持数值离散梯度和数值连续四面体梯度两种方法
- **共享资源**：采样器和单位 AABB 缓冲区在所有实例间通过 `SharedCache` 共享

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `NDSDFGrid.h` | 头文件 | `NDSDFGrid` 类声明，继承自 `SDFGrid`，定义多级纹理和归一化参数 |
| `NDSDFGrid.cpp` | 源文件 | CPU 端实现：资源创建（3D 纹理）、着色器数据绑定、距离值归一化转换 |
| `NDSDFGrid.slang` | Slang着色器 | GPU 端实现：自适应 LOD 球追踪求交算法、梯度计算、体素坐标计算 |

## 依赖关系

### 内部依赖
- `Scene/SDFs/SDFGrid.h` - SDF 网格基类
- `Scene/SDFs/SDFGridBase` - Slang 侧 SDF 网格基础接口
- `Scene/SDFs/SDFVoxelCommon` - 体素公共工具（三线性插值、表面检测等）
- `Scene/SDFs/SDFVoxelHitUtils` - 体素求交工具（精确体素内求交、数值梯度计算）
- `Core/API/Texture.h` - 3D 纹理资源
- `Core/API/RenderContext.h` - 渲染上下文（纹理数据更新）
- `Utils/SharedCache.h` - 共享资源缓存
- `Utils/Geometry/IntersectionHelpers` - AABB 光线求交
- `Utils/Math/MathConstants.slangh` - 数学常量（如 `M_SQRT3`）
