# ColorUtils 源码文档

> 路径: `Source/Falcor/Utils/Color/ColorUtils.h`
> 类型: C++ 头文件 (仅头文件实现)
> 模块: Utils/Color

## 功能概述

本文件提供了主机端 (CPU) 的颜色空间转换工具函数和相关常量矩阵。主要覆盖 sRGB (Rec.709)、CIE XYZ、LMS (CAT02 和 Bradford 变换) 之间的转换,以及色温到 XYZ 颜色的转换和白平衡矩阵的计算。Falcor 默认假设所有输入/输出位于 sRGB (ITU-R Rec. BT.709) 色彩空间中。

## 类与接口

本文件不定义类,全部为 `Falcor` 命名空间下的静态函数和常量。

## 常量矩阵

| 常量名 | 类型 | 说明 |
|--------|------|------|
| `kColorTransform_RGBtoXYZ_Rec709` | `float3x3` | Rec.709 RGB 到 CIE XYZ 变换矩阵 |
| `kColorTransform_XYZtoRGB_Rec709` | `float3x3` | CIE XYZ 到 Rec.709 RGB 变换矩阵 |
| `kColorTransform_XYZtoLMS_CAT02` | `float3x3` | CIE XYZ 到 LMS 的 CAT02 变换矩阵 |
| `kColorTransform_LMStoXYZ_CAT02` | `float3x3` | LMS 到 CIE XYZ 的 CAT02 逆变换矩阵 |
| `kColorTransform_XYZtoLMS_Bradford` | `float3x3` | CIE XYZ 到 LMS 的 Bradford 变换矩阵 |
| `kColorTransform_LMStoXYZ_Bradford` | `float3x3` | LMS 到 CIE XYZ 的 Bradford 逆变换矩阵 |

## 关键函数

| 函数签名 | 说明 |
|----------|------|
| `float3 RGBtoXYZ_Rec709(float3 c)` | 将 Rec.709 RGB 颜色转换为 CIE XYZ |
| `float3 XYZtoRGB_Rec709(float3 c)` | 将 CIE XYZ 颜色转换为 Rec.709 RGB |
| `float3 xyYtoXYZ(float x, float y, float Y)` | 将 CIE xyY 色度坐标+亮度转换为 XYZ |
| `float3 colorTemperatureToXYZ(float T, float Y)` | 将色温 (开尔文) 转换为 CIE XYZ 颜色。支持 1667K-25000K 范围。基于 Kang 等人 (2002) 的分段有理多项式近似。 |
| `float3x3 calculateWhiteBalanceTransformRGB_Rec709(float T)` | 计算 Rec.709 RGB 空间中的白平衡 3x3 变换矩阵。使用 von Kries 变换 (LMS 空间对角缩放)。以 D65 (6500K) 为参考白点。 |

## 依赖关系

### 本文件引用
- `Core/Error.h` - 错误检查宏 (`FALCOR_CHECK`)
- `Utils/Math/Vector.h` - 向量类型 (`float3`)
- `Utils/Math/Matrix.h` - 矩阵类型 (`float3x3`) 和矩阵运算 (`mul`, `math::matrixFromDiagonal`)

### 被以下文件引用
- `Source/Falcor/Utils/Color/Spectrum.h` - 光谱转 RGB 时使用 `XYZtoRGB_Rec709`
- `Source/Falcor/Utils/Color/SpectrumUtils.h` - 光谱工具类引用本文件
- `Source/Falcor/Utils/Color/SpectrumUtils.cpp` - 波长转 RGB 实现

## 实现细节

- 所有函数和常量均声明为 `static`,实现在头文件中 (header-only)。
- 色温转换使用 Kang 等人 (2002) 的 HDTV 色温控制论文中的分段有理多项式近似,首先计算 CIE xy 色度坐标,然后转换为 XYZ。
- 白平衡变换通过 CAT02 色彩适应变换实现:先将 RGB 变换到 LMS 空间,在 LMS 空间进行对角缩放 (von Kries 变换),再变换回 RGB 空间。目标白点为 D65 (6500K)。
- 注意:白平衡后的 RGB 值可能超出 Rec.709 色域范围 (出现负值),建议对结果进行色域裁剪。
