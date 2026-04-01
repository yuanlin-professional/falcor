# MathHelpers.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/MathHelpers.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

GPU 端核心数学工具函数库，包含球面坐标映射、八面体映射、各种采样函数、矩阵求逆、特殊函数等。是 Falcor 着色器系统中最常被引用的数学模块之一。

## 函数

### 球面坐标
| 函数签名 | 说明 |
|----------|------|
| `float2 cartesian_to_spherical_unorm(float3 p)` | 笛卡尔坐标转归一化球面坐标 |
| `float2 cartesian_to_spherical_rad(float3 p)` | 笛卡尔坐标转弧度球面坐标 |
| `float3 spherical_to_cartesian_rad(float2 sph)` | 弧度球面坐标转笛卡尔坐标 |
| `float2 world_to_latlong_map(float3 dir)` | 世界方向转经纬度图坐标 |
| `float3 latlong_map_to_world(float2 latlong)` | 经纬度图坐标转世界方向 |

### 八面体映射
| 函数签名 | 说明 |
|----------|------|
| `float2 ndir_to_oct_snorm(float3 n)` | 方向转八面体映射（有符号归一化） |
| `float2 ndir_to_oct_unorm(float3 n)` | 方向转八面体映射（无符号归一化） |
| `float3 oct_to_ndir_snorm(float2 p)` | 八面体映射转方向（有符号） |
| `float3 oct_to_ndir_unorm(float2 p)` | 八面体映射转方向（无符号） |
| `float2 ndir_to_oct_equal_area_unorm(float3 n)` | 等面积八面体映射编码 |
| `float3 oct_to_ndir_equal_area_unorm(float2 p)` | 等面积八面体映射解码 |

### 采样函数
| 函数签名 | 说明 |
|----------|------|
| `float2 sample_disk(float2 u)` | 均匀圆盘采样 |
| `float3 sample_cone(float2 u, float cosTheta)` | 锥体内均匀方向采样 |
| `float3 sample_sphere(float2 u)` | 均匀球面采样 |
| `float3 sample_hemisphere(float2 u)` | 均匀半球采样 |
| `float2 sample_disk_concentric(float2 u)` | Shirley 同心映射圆盘采样 |
| `float3 sample_cosine_hemisphere_concentric(float2 u, out float pdf)` | 余弦加权半球采样（同心映射） |
| `float3 sample_cosine_hemisphere_polar(float2 u, out float pdf)` | 余弦加权半球采样（极坐标） |
| `float3 sample_triangle(float2 u)` | 三角形均匀采样 |
| `float3 sampleVonMisesFisher(float2 u, float kappa)` | von Mises-Fisher 分布采样 |

### 矩阵求逆
| 函数签名 | 说明 |
|----------|------|
| `float2x2 inverse(float2x2 M)` | 2x2 矩阵求逆 |
| `float2x3 inverse(float2x3 M)` | 2x3 矩阵求逆 |
| `float3x3 inverse(float3x3 M)` | 3x3 矩阵求逆 |
| `float4x4 inverse(float4x4 m)` | 4x4 矩阵求逆 |

### 特殊函数
| 函数签名 | 说明 |
|----------|------|
| `float erf(float x)` | 误差函数 |
| `float erfinv(float x)` | 反误差函数 |
| `float lgamma(float x)` | 对数 Gamma 函数 |
| `float gamma(float x)` | Gamma 函数 |
| `float beta(float x, float y)` | Beta 函数 |

### 其他工具
| 函数签名 | 说明 |
|----------|------|
| `float3 perp_stark(float3 u)` | 正交向量生成 |
| `void branchlessONB(...)` | 无分支正交基 |
| `float sqr(float v)` | 平方（可微分） |
| `float2 calcMotionVector(...)` | 计算屏幕空间运动向量 |
| `void orthogonalizeVectors(...)` | 向量正交化 |
| `float3 rotate_x/y/z(...)` | 绕轴旋转（可微分） |
| `float3 normalizeSafe(float3 v)` | 安全归一化（零向量返回零） |

## 依赖关系
### import
- `Utils/Math/MathConstants.slangh`（通过 `#include`）
