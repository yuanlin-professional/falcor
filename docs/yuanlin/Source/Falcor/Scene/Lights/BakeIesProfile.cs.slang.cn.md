# BakeIesProfile.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/BakeIesProfile.cs.slang`
> 类型: Slang 计算着色器
> 模块: Scene/Lights

## 功能概述

计算着色器，用于将 IES 光源配置文件数据烘焙为 2D 纹理。每个线程处理一个纹素，根据垂直角和水平角在 IES 数据中进行双线性插值查找光强值。同时计算通量纹理用于后续的通量因子积分。

## 结构体与接口

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gIesData` | `Buffer<float>` | IES 原始数值数据缓冲区 |
| `gTexture` | `RWTexture2D<float>` | 输出光强纹理 |
| `gFluxTexture` | `RWTexture2D<float>` | 输出通量纹理 |
| `gBakeResolution` | `uint` (cbuffer) | 烘焙分辨率 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float findAngleIndex(float angle, int offset, int count)` | 在角度数组中查找并线性插值索引 |
| `void main(uint2 threadID)` | 主入口：计算每个纹素对应的垂直/水平角，查找 IES 数据，输出光强和通量 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量（M_PI）

## 实现细节

- 线程组大小：16x16x1。
- 垂直角范围：0-180 度，水平角范围：-180 到 180 度。
- 支持 IES 水平对称性：当最大水平角 <= 180 度时应用对称处理。
- 通量计算：`result * sin(theta) * 2 * PI^2 / resolution^2`，所有纹素之和即为总通量积分。
