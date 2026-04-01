# Spectrum 源码文档

> 路径: `Source/Falcor/Utils/Color/Spectrum.h` + `Source/Falcor/Utils/Color/Spectrum.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Color

## 功能概述

本文件定义了多种光谱表示类型,包括分段线性插值光谱 (`PiecewiseLinearSpectrum`)、密集采样光谱 (`DenseleySampledSpectrum`) 和黑体辐射光谱 (`BlackbodySpectrum`)。同时提供了光谱的内积计算、光谱到 CIE XYZ / RGB 的转换函数,以及一个包含大量预定义命名光谱 (玻璃、金属、标准光源、相机响应曲线) 的集合类 `Spectra`。

## 类与接口

### `PiecewiseLinearSpectrum`

- **继承**: 无
- **职责**: 表示分段线性插值的光谱。存储非均匀分布的波长-值对,支持二分查找插值求值。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PiecewiseLinearSpectrum(span<const float> wavelengths, span<const float> values)` | 从波长和值数组构造光谱 |
| `static PiecewiseLinearSpectrum fromInterleaved(span<const float> interleaved, bool normalize)` | 从交错数据创建光谱。可选归一化使亮度为 1。 |
| `static optional<PiecewiseLinearSpectrum> fromFile(const path& path)` | 从文本文件加载光谱 (未实现) |
| `void scale(float factor)` | 按常数因子缩放所有光谱值 |
| `float eval(float wavelength) const` | 在指定波长处求值。波长范围外返回 0。使用二分查找 + 线性插值。 |
| `float2 getWavelengthRange() const` | 获取波长范围 |
| `float getMaxValue() const` | 获取光谱最大值 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mWavelengths` | `vector<float>` | 波长数组 (nm,递增排列) |
| `mValues` | `vector<float>` | 各波长处的值 |
| `mMaxValue` | `float` | 值的最大值 |

---

### `DenseleySampledSpectrum`

- **继承**: 无
- **职责**: 表示密集均匀采样的光谱,适合快速查表求值。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `DenseleySampledSpectrum(float minWavelength, float maxWavelength, span<const float> values)` | 从均匀采样数据构造 |
| `DenseleySampledSpectrum(const S& spectrum, float wavelengthStep)` | 从任意光谱类型重采样构造 (模板构造函数) |
| `float eval(float wavelength) const` | 最近邻查表求值,超出范围返回 0 |
| `float2 getWavelengthRange() const` | 获取波长范围 |
| `float getMaxValue() const` | 获取光谱最大值 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mMinWavelength` | `float` | 最小波长 (nm) |
| `mMaxWavelength` | `float` | 最大波长 (nm) |
| `mWavelengthStep` | `float` | 采样间隔 (nm) |
| `mValues` | `vector<float>` | 采样值数组 |
| `mMaxValue` | `float` | 最大采样值 |

---

### `BlackbodySpectrum`

- **继承**: 无
- **职责**: 表示黑体辐射光谱,基于普朗克定律计算给定温度下任意波长的辐射值。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BlackbodySpectrum(float temperature, bool normalize)` | 构造黑体光谱。可选归一化使峰值为 1。 |
| `float eval(float wavelength) const` | 计算指定波长的黑体辐射值 |
| `float2 getWavelengthRange() const` | 返回 (-inf, +inf),黑体光谱定义域无限 |
| `float getMaxValue() const` | 获取峰值 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mTemperature` | `float` | 温度 (开尔文) |
| `mNormalizationFactor` | `float` | 归一化因子 |
| `mMaxValue` | `float` | 峰值 |

---

### `Spectra`

- **继承**: 无 (结构体)
- **职责**: 包含预定义的命名光谱集合和 CIE 颜色匹配函数。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `kCIE_X` | `DenseleySampledSpectrum` | CIE 1931 X 颜色匹配函数 (360-830nm) |
| `kCIE_Y` | `DenseleySampledSpectrum` | CIE 1931 Y 颜色匹配函数 |
| `kCIE_Z` | `DenseleySampledSpectrum` | CIE 1931 Z 颜色匹配函数 |
| `kCIE_Y_Integral` | `float` (constexpr) | CIE Y 函数积分值 = 106.856895 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static const PiecewiseLinearSpectrum* getNamedSpectrum(const string& name)` | 按名称获取预定义光谱,未找到返回 nullptr |

## 自由函数

| 函数签名 | 说明 |
|----------|------|
| `float blackbodyEmission(float wavelength, float temperature)` | 计算黑体辐射亮度 (普朗克定律) |
| `float innerProduct(const A& a, const B& b)` | 计算两个光谱的内积 (波长重叠区间上的数值积分) |
| `float3 spectrumToXYZ(const S& s)` | 将光谱转换为 CIE 1931 XYZ 三刺激值 |
| `float3 spectrumToRGB(const S& s)` | 将光谱转换为 Rec.709 RGB |

## 预定义命名光谱列表

实现文件中通过 `kNamedSpectra` 哈希表注册了以下命名光谱:
- **玻璃**: glass-BK7, glass-BAF10, glass-FK51A, glass-LASF9, glass-F5, glass-F10, glass-F11
- **金属 eta/k**: metal-Ag, metal-Al, metal-Au, metal-Cu, metal-CuZn, metal-MgO, metal-TiO2
- **标准光源**: stdillum-A, stdillum-D50, stdillum-D65, stdillum-F1~F12, illum-acesD60
- **相机响应**: canon_eos_100d, canon_eos_1dx_mkii, canon_eos_200d, canon_eos_5d 系列, hasselblad_l1d_20c, nikon_d810/d850, sony_ilce 系列 (各含 _r/_g/_b 三通道)

## 依赖关系

### 本文件引用
- `Core/Macros.h` - 导出宏 (`FALCOR_API`)
- `Core/Error.h` - 错误检查
- `Utils/Math/Common.h` - 数学工具 (`math::lerp`)
- `Utils/Math/Vector.h` - 向量类型
- `Utils/Color/ColorUtils.h` - `XYZtoRGB_Rec709` 函数
- `Spectra.inl` - 光谱原始数据 (在 Spectrum.cpp 中引入)
- `<fstd/span.h>` - span 类型

### 被以下文件引用
- `Source/Falcor/Utils/Color/SpectrumUtils.h` - 间接使用
- 渲染管线中需要光谱数据的模块

## 实现细节

- `PiecewiseLinearSpectrum::eval` 使用 `std::lower_bound` 进行二分查找,然后线性插值。时间复杂度 O(log n)。
- `DenseleySampledSpectrum::eval` 使用 `std::lroundf` 计算最近邻索引进行查表。时间复杂度 O(1)。
- `blackbodyEmission` 使用普朗克黑体辐射公式,物理常数 c (光速)、h (普朗克常数)、kb (玻尔兹曼常数) 均使用 SI 单位。
- `BlackbodySpectrum` 构造时通过维恩位移定律计算峰值波长,用于归一化。
- `innerProduct` 以 1nm 步长在两个光谱的波长重叠区间上进行数值积分 (矩形求积)。
- `spectrumToXYZ` 利用 CIE 颜色匹配函数与待转光谱的内积,并除以 `kCIE_Y_Integral` 进行归一化。
