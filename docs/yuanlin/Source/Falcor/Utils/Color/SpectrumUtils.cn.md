# SpectrumUtils 源码文档

> 路径: `Source/Falcor/Utils/Color/SpectrumUtils.h` + `Source/Falcor/Utils/Color/SpectrumUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Color

## 功能概述

本文件定义了 `SpectrumUtils` 工具类,提供波长到 CIE XYZ、D65 光源值和 RGB 的转换功能,以及基于 `SampledSpectrum` 的光谱积分和色彩空间转换模板方法。该类持有预加载的 CIE 1931 颜色匹配函数 (1nm 采样) 和 D65 标准光源 (5nm 采样) 的静态数据。

## 类与接口

### `SpectrumUtils`

- **继承**: 无
- **职责**: 提供波长到颜色的转换、光谱积分和光谱到 RGB 的完整转换流水线

#### 静态数据成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sCIE_XYZ_1931_1nm` | `SampledSpectrum<float3>` | CIE 1931 XYZ 颜色匹配函数,360-830nm,1nm 间隔,471 个采样点 |
| `sD65_5nm` | `SampledSpectrum<float>` | D65 标准光源光谱,300-830nm,5nm 间隔,107 个采样点 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static float3 wavelengthToXYZ_CIE1931(float lambda)` | 将波长转为 CIE 1931 XYZ 三刺激值。使用 1nm 采样曲线线性插值。 |
| `static float wavelengthToD65(float lambda)` | 将波长转为 D65 标准光源辐射值。使用 5nm 采样曲线线性插值。 |
| `static float3 wavelengthToRGB_Rec709(float lambda)` | 将波长转为 Rec.709 RGB 颜色。先转 XYZ 再转 RGB。 |
| `static ReturnType integrate(SampledSpectrum<T>&, SpectrumInterpolation, func, componentIndex, integrationSteps)` | 对光谱进行数值积分,每步应用用户自定义函数。使用梯形求积法 (端点半权重)。 |
| `static float3 toXYZ(SampledSpectrum<T>&, ...)` | 将光谱转换为 CIE XYZ。通过 `integrate` 与 CIE XYZ 匹配函数求内积。 |
| `static float3 toXYZ_D65(SampledSpectrum<T>&, ...)` | 将光谱在 D65 光源下转换为 CIE XYZ。积分中同时乘以 D65 光源值。 |
| `static float3 toRGB_D65(SampledSpectrum<T>&, ...)` | 将光谱在 D65 光源下转换为 RGB。基于 JCGT 论文 "An OpenEXR Layout for Spectral Images" 的公式 8,除以 Y_D65 = 10567.0762。 |

## 依赖关系

### 本文件引用
- `SampledSpectrum.h` - 均匀采样光谱类
- `Core/Macros.h` - 导出宏
- `Core/Error.h` - 断言
- `Utils/Math/Vector.h` - 向量类型
- `Utils/Color/ColorUtils.h` - `XYZtoRGB_Rec709` 转换
- `<xyzcurves/ciexyzCurves1931_1nm.h>` - CIE XYZ 原始数据 (实现文件中)
- `<illuminants/D65_5nm.h>` - D65 光源原始数据 (实现文件中)
- `<algorithm>`, `<functional>`, `<type_traits>` - 标准库

### 被以下文件引用
- 需要进行光谱到 RGB 转换的渲染管线模块

## 实现细节

- `integrate` 方法使用黎曼求和 (梯形法则):首尾采样点权重为 0.5,其余为 1.0,乘以波长步长 `waveLengthDelta`。支持在每两个采样点之间插入额外的积分步 (`integrationSteps` 参数) 以提高精度。
- 当光谱类型 `T` 为向量类型时 (如 `float3`),`componentIndex` 参数指定积分哪个分量。对标量类型 (`float`) 则直接使用值。
- `toRGB_D65` 基于论文 "An OpenEXR Layout for Spectral Images" (JCGT 2021) 的方程 8,归一化常数 `Y_D65 = 10567.0762` 由 D65 光源光谱的 Y 分量积分计算得出。
- 静态数据成员在 `SpectrumUtils.cpp` 中初始化,通过 `reinterpret_cast` 将原始浮点数组转换为对应的 `SampledSpectrum` 类型。
