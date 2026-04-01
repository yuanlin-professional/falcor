# Types 源码文档

> 路径: `Source/Falcor/Core/API/Types.h` / `Types.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Types 模块定义了 Falcor 使用的基础图形类型枚举，包括着色器模型、着色器类型、数据类型和比较函数。

## 类与接口

### `ShaderModel`（枚举）
着色器模型版本：Unknown、SM6_0 至 SM6_7。

辅助函数：
- `getShaderModelMajorVersion(ShaderModel)` — 获取主版本号
- `getShaderModelMinorVersion(ShaderModel)` — 获取次版本号

### `ShaderType`（枚举）
着色器类型：
| 值 | 说明 |
|----|------|
| `Vertex` | 顶点着色器 |
| `Pixel` | 像素着色器 |
| `Geometry` | 几何着色器 |
| `Hull` | 外壳着色器（曲面细分控制） |
| `Domain` | 域着色器（曲面细分求值） |
| `Compute` | 计算着色器 |
| `RayGeneration` | 射线生成着色器 |
| `Intersection` | 求交着色器 |
| `AnyHit` | 任意命中着色器 |
| `ClosestHit` | 最近命中着色器 |
| `Miss` | 未命中着色器 |
| `Callable` | 可调用着色器 |

### `DataType`（枚举）
数据类型：int8/16/32/64、uint8/16/32/64、float16/32/64。

### `ComparisonFunc`（枚举）
比较函数：Disabled、Never、Always、Less、Equal、NotEqual、LessEqual、Greater、GreaterEqual。

## 依赖关系
### 本文件引用
- `Core/Enum.h`

### 被以下文件引用
- `DepthStencilState.h`, `Sampler.h`, `Device.h`
