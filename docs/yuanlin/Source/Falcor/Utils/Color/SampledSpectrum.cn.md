# SampledSpectrum 源码文档

> 路径: `Source/Falcor/Utils/Color/SampledSpectrum.h`
> 类型: C++ 头文件 (模板类,仅头文件实现)
> 模块: Utils/Color

## 功能概述

本文件定义了 `SampledSpectrum<T>` 模板类,表示均匀采样的光谱数据。支持以浮点标量或向量类型存储每个波长采样点的值,提供基于分段线性插值的光谱求值能力。该类是光谱渲染中光谱数据表示的基础组件。

## 类与接口

### `SampledSpectrum<T>`

- **继承**: 无
- **模板参数**: `T` - 采样值类型,必须为 `float`、`float2`、`float3` 或 `float4` 之一
- **职责**: 存储和求值均匀采样的光谱数据

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SampledSpectrum(float lambdaStart, float lambdaEnd, size_t sampleCount)` | 构造零初始化的光谱,指定波长范围和采样数 |
| `SampledSpectrum(float lambdaStart, float lambdaEnd, size_t sampleCount, const value_type* pSamples)` | 从采样数组构造光谱 |
| `void set(const size_t sampleCount, const value_type* pSamples)` | 用数组设置光谱采样值 |
| `void set(const std::vector<value_type>& samples)` | 用 vector 设置光谱采样值 |
| `void set(const size_t sampleCount, const value_type* pSamples, const float* pLambdas)` | 从非均匀采样数据设置 (未实现) |
| `value_type eval(float lambda, SpectrumInterpolation interpolationType) const` | 在指定波长处求值光谱。使用分段线性插值。波长超出范围返回零。 |
| `float3 toXYZ_CIE1931() const` | 返回光谱的 CIE 1931 三刺激值 (未实现) |
| `size_t size() const` | 返回采样点数量 |
| `value_type get(size_t index) const` | 按索引获取采样值 |
| `void set(size_t index, T value)` | 按索引设置采样值 |
| `float2 getWavelengthRange() const` | 返回波长范围 [lambdaStart, lambdaEnd] |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mLambdaStart` | `float` | 首个波长采样点 (nm) |
| `mLambdaEnd` | `float` | 末尾波长采样点 (nm) |
| `mSamples` | `std::vector<value_type>` | 光谱采样值数组 |

## 枚举

### `SpectrumInterpolation`

| 枚举值 | 说明 |
|--------|------|
| `Linear` | 分段线性插值,端点外返回零 |

## 依赖关系

### 本文件引用
- `Core/Error.h` - 错误检查宏 (`FALCOR_CHECK`, `FALCOR_ASSERT`, `FALCOR_UNIMPLEMENTED`)
- `Utils/Math/Common.h` - 数学工具 (`math::lerp`)
- `Utils/Math/Vector.h` - 向量类型 (`float2`, `float3`, `float4`)
- `<type_traits>` - 类型萃取
- `<vector>` - 标准容器
- `<cmath>` - 数学函数 (`std::floor`)

### 被以下文件引用
- `Source/Falcor/Utils/Color/SpectrumUtils.h` - `SpectrumUtils` 类使用 `SampledSpectrum<float3>` 和 `SampledSpectrum<float>` 作为静态成员

## 实现细节

- 采样点均匀分布在 [lambdaStart, lambdaEnd] 范围内。例如 lambdaStart=400nm, lambdaEnd=700nm, sampleCount=4 时,采样中心分别在 400, 500, 600, 700nm。
- `eval` 方法通过计算波长在采样范围内的归一化位置,找到相邻两个采样点进行线性插值。
- 从非均匀采样数据设置 (`set` 带波长数组的重载) 标记为 `FALCOR_UNIMPLEMENTED`,尚未实现。
- `toXYZ_CIE1931` 同样标记为 `FALCOR_UNIMPLEMENTED`。
