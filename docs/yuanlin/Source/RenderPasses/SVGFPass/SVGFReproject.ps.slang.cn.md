# SVGFReproject.ps.slang 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFReproject.ps.slang`
> 类型: Slang 像素着色器
> 模块: RenderPasses/SVGFPass

## 功能概述

实现 SVGF 的时间重投影阶段。根据运动矢量将前一帧的滤波光照和矩重投影到当前帧，通过双线性插值和跨双边验证确保重投影的有效性。当双线性采样点无效时，回退到 3x3 跨双边滤波搜索有效样本。输出当前帧的时间混合光照、矩和历史长度。

## 结构体与接口

### `PS_OUT`

| 成员 | 语义 | 说明 |
|------|------|------|
| `OutIllumination` | SV_TARGET0 | 光照（RGB）+ 方差（A） |
| `OutMoments` | SV_TARGET1 | 一阶和二阶矩 |
| `OutHistoryLength` | SV_TARGET2 | 历史长度 |

## 函数

| 函数 | 说明 |
|------|------|
| `demodulate(float3 c, float3 albedo)` | 解调颜色：c / max(albedo, 0.001) |
| `isReprjValid(coord, Z, Zprev, fwidthZ, normal, normalPrev, fwidthNormal)` | 验证重投影像素的有效性（边界、深度偏差、法线兼容性） |
| `loadPrevData(float2, out float4, out float2, out float)` | 加载并插值前一帧数据（双线性 + 回退跨双边） |
| `computeVarianceScale(...)` | 方差缩放（未使用） |
| `main(FullScreenPassVsOut)` | 重投影主入口，计算时间混合光照和矩 |

## 依赖关系 / import

- `Utils.Math.MathHelpers` — 八面体编码解码
- `Utils.Color.ColorHelpers` — 亮度计算
- `SVGFCommon` — 公共模块
