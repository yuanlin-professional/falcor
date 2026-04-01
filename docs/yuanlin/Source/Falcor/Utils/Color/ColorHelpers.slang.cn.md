# ColorHelpers.slang 源码文档

> 路径: `Source/Falcor/Utils/Color/ColorHelpers.slang`
> 类型: Slang 着色器文件 (主机/设备共享)
> 模块: Utils/Color

## 功能概述

本文件提供了主机端 (CPU) 和设备端 (GPU) 共享的颜色空间转换工具函数集合。涵盖了多种常用颜色空间之间的相互转换,包括 RGB、XYZ、YCgCo、YUV、sRGB、CIE Lab、Oklab、HCL、YCxCz 等。文件通过 `HOST_CODE` 宏区分主机端和设备端代码路径,部分函数支持 Slang 的可微分 (`[Differentiable]` / `[BackwardDifferentiable]`) 特性。

## 常量

| 常量名 | 类型 | 说明 |
|--------|------|------|
| `kD65ReferenceIlluminant` | `float3` | D65 标准光源参考白点 XYZ 值 |
| `kInvD65ReferenceIlluminant` | `float3` | D65 标准光源参考白点 XYZ 值的倒数 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float luminance(float3 rgb)` | 计算 ITU-R BT.709 线性 RGB 颜色的相对亮度 (Y)。支持可微分。 |
| `float3 RGBtoXYZ_Rec709(float3 c)` | 将 Rec.709 RGB 颜色转换为 CIE XYZ 色彩空间 (仅设备端) |
| `float3 XYZtoRGB_Rec709(float3 c)` | 将 CIE XYZ 颜色转换为 Rec.709 RGB 色彩空间 (仅设备端) |
| `float3 RGBToYCgCo(float3 rgb)` | RGB 转 YCgCo 颜色空间 |
| `float3 YCgCoToRGB(float3 YCgCo)` | YCgCo 转 RGB 颜色空间 |
| `float3 RGBToYUV(float3 rgb)` | ITU-R BT.709 RGB 转 YUV |
| `float3 YUVToRGB(float3 yuv)` | ITU-R BT.709 YUV 转 RGB |
| `float sRGBToLinear(float srgb)` | sRGB 单通道转线性空间 (含 gamma 校正) |
| `float3 sRGBToLinear(float3 srgb)` | sRGB 三通道转线性空间 |
| `float linearToSRGB(float lin)` | 线性空间单通道转 sRGB |
| `float3 linearToSRGB(float3 lin)` | 线性空间三通道转 sRGB |
| `float computeMichelsonContrast(float iMin, float iMax)` | 计算 Michelson 对比度 |
| `float3 linearRGBToXYZ(float3 linColor)` | 线性 RGB 转 CIE XYZ (D65 光源)。支持反向可微分。 |
| `float3 XYZToLinearRGB(float3 xyzColor)` | CIE XYZ 转线性 RGB (D65 光源)。支持反向可微分。 |
| `float3 XYZToOklab(float3 xyzColor)` | CIE XYZ 转 Oklab 颜色空间 (仅设备端)。支持反向可微分。 |
| `float3 OklabToXYZ(float3 oklabColor)` | Oklab 转 CIE XYZ (仅设备端)。支持反向可微分。 |
| `float3 OklabToHCL(float3 oklabColor)` | Oklab 转 HCL (色相-色度-亮度) (仅设备端)。支持反向可微分。 |
| `float3 HCLToOklab(float3 hclColor)` | HCL 转 Oklab (仅设备端)。支持反向可微分。 |
| `float3 RGBToOklab(float3 lColor)` | 线性 RGB 转 Oklab (仅设备端)。支持反向可微分。 |
| `float3 OklabToRGB(float3 lab)` | Oklab 转线性 RGB (仅设备端)。支持反向可微分。 |
| `float3 XYZToCIELab(float3 xyzColor, float3 invReferenceIlluminant)` | CIE XYZ 转 CIE Lab (默认 D65 光源) |
| `float3 CIELabToXYZ(float3 labColor, float3 referenceIlluminant)` | CIE Lab 转 CIE XYZ (默认 D65 光源) |
| `float3 XYZToYCxCz(float3 xyzColor, float3 invReferenceIlluminant)` | CIE XYZ 转 YCxCz |
| `float3 YCxCzToXYZ(float3 ycxczColor, float3 referenceIlluminant)` | YCxCz 转 CIE XYZ |
| `float3 linearRGBToCIELab(float3 lColor)` | 线性 RGB 转 CIE Lab |
| `float3 YCxCzToLinearRGB(float3 ycxczColor)` | YCxCz 转线性 RGB |
| `float3 linearRGBToYCxCz(float3 lColor)` | 线性 RGB 转 YCxCz |

## 依赖关系

### import / include
- `Utils/HostDeviceShared.slangh` - 主机/设备共享宏定义 (`BEGIN_NAMESPACE_FALCOR`, `HOST_CODE` 等)

### 被以下文件引用
- 渲染管线中需要颜色空间转换的各类着色器

## 实现细节

- 使用 `HOST_CODE` 宏区分主机端和设备端代码。主机端函数使用 `inline` 关键字,设备端函数使用 Slang 的 `[Differentiable]` 或 `[BackwardDifferentiable]` 属性标注以支持自动微分。
- `actualPow` 辅助函数处理负数输入的幂运算,用于 Oklab 转换中避免 `pow` 对负数的未定义行为。
- sRGB 与线性空间的转换使用标准的分段函数:线性段阈值 0.04045 (sRGB -> 线性) 和 0.0031308 (线性 -> sRGB)。
- RGB 到 XYZ 的转换矩阵基于 ITU-R BT.709 原色和 D65 白点推导而来。
- Oklab 转换基于 Bjorn Ottosson 的 Oklab 色彩空间定义,使用两级矩阵变换 (XYZ -> LMS -> Oklab)。
