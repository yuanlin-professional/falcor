# PackRadiance.cs.slang 源码文档

> 路径: `Source/RenderPasses/NRDPass/PackRadiance.cs.slang`
> 类型: Slang 计算着色器
> 模块: RenderPasses/NRDPass

## 功能概述

NRD 降噪通道的辐射度打包计算着色器。将路径追踪器输出的原始漫反射和镜面辐射度数据转换为 NRD 所需的打包格式。支持 ReLAX 和 ReBLUR 两种打包方法。在打包前对辐射度进行亮度钳制（限制最大强度），以避免降噪器中的数值问题。

## 函数

| 函数 | 说明 |
|------|------|
| `clampRadiance(inout float3, inout float3)` | 对漫反射和镜面辐射度进行亮度钳制 |
| `main(uint3 dispatchThreadId)` | 主入口。读取辐射度和命中距离，钳制后按 NRD 方法打包写回 |

## 依赖关系 / import

- `nrd/Shaders/Include/NRD.hlsli` — NRD 着色器库（提供 `RELAX_FrontEnd_PackRadianceAndHitDist` 和 `REBLUR_FrontEnd_*` 函数）
- `Utils.Color.ColorHelpers` — 亮度计算

## 实现细节

- 通过编译时宏 `NRD_METHOD` 区分 ReLAX 和 ReBLUR 两种打包模式
- ReLAX 模式直接打包辐射度和命中距离
- ReBLUR 模式额外需要 viewZ 和线性粗糙度来归一化命中距离
- 辐射度钳制阈值通过 `gMaxIntensity` 常量缓冲区传入
