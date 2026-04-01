# IBSDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/IBSDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

定义低级别双向散射分布函数（BSDF）接口 `IBSDF`，以及相关辅助结构体。所有操作在局部坐标系中进行（N=(0,0,1), T=(1,0,0), B=(0,1,0)），入射和出射方向均指向远离着色点。

## 结构体与接口

### `IBSDF`（接口）

- **anyValueSize**: 96 字节
- **职责**: BSDF 的求值、采样和 PDF 计算的通用接口

| 方法签名 | 说明 |
|----------|------|
| `eval(wi, wo, sg, bc)` | 求值 f(wi,wo)*dot(wo,n) |
| `sample(wi, wo, pdf, weight, lobeType, sg, bc)` | 采样出射方向 |
| `evalPdf(wi, wo, bc)` | 计算方向 PDF |
| `evalAlbedo(wi, lobetype)` | 计算半球反照率（四分量） |
| `getIorAsReflectance()` | 获取法线入射反射率形式的 IOR |
| `getRoughnessInformation(wi)` | 获取粗糙度信息 |

### `AlbedoContributions`

反照率分为四个分量，总和应为 1.0：
- `reflection` - 上半球反射能量
- `absorptionR` - 上半球吸收能量
- `transmission` - 下半球透射能量
- `absorptionT` - 下半球吸收能量

### `BSDFContext`

BSDF 上下文，包含入射/透射介质的 IOR。

### `RoughnessInformation`

粗糙度信息结构体，包含 BSDF 符号表示的粗糙度。

## 依赖关系

### export
- `Rendering.Materials.LobeType`
- `Utils.Sampling.SampleGeneratorInterface`
