# SpectrumUtils.slang 源码文档

> 路径: `Source/Falcor/Utils/Color/SpectrumUtils.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Color

## 功能概述

本文件提供了 GPU 着色器端的光谱工具,用于将波长转换为 CIE 1931 XYZ 三刺激值。使用 Wyman 等人 (2013) 提出的多瓣分段高斯拟合方法,避免在着色器中存储完整的颜色匹配函数查找表。

## 结构体与接口

### `SpectrumUtils`

- **职责**: 提供波长到 XYZ 颜色匹配函数值的 GPU 端计算

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `static float3 wavelengthToXYZ(float lambda)` | 将波长 (nm) 转换为 CIE 1931 XYZ 三刺激值。使用解析近似公式。 |
| `static float G(float x, float lambda, float invSigma1, float invSigma2)` | 内部辅助函数。计算非对称高斯基函数值。 |

## 依赖关系

### import / include
- 无外部依赖 (自包含文件)

### 被以下文件引用
- 需要在着色器中进行波长到颜色转换的渲染模块 (如光谱渲染、色散效果等)

## 实现细节

- 基于 Wyman, Sloan, Shirley 的论文 "Simple Analytic Approximations to the CIE XYZ Color Matching Functions" (JCGT, vol 2(2), 2013)。
- X 匹配函数拟合为三个高斯瓣的组合 (中心波长 442.0, 599.8, 501.1nm),Y 拟合为两个 (568.8, 530.9nm),Z 拟合为两个 (437.0, 459.0nm)。
- `G` 函数为非对称高斯函数:当 `x < lambda` 时使用 `invSigma1`,否则使用 `invSigma2`,实现左右不对称的钟形曲线拟合。
- 与 CPU 端的 `SpectrumUtils` (基于查表插值) 不同,GPU 端使用解析近似以获得更好的计算性能,适合并行着色器执行。
