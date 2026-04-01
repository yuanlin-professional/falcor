# GuideData.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/GuideData.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

定义了降噪引导数据结构 `GuideData`，用于存储路径追踪器生成的每样本降噪辅助信息。数据使用紧凑的打包格式存储反照率、引导法线、镜面反照率、间接反照率和反射位置等信息。

## 结构体与接口

### `GuideData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `data` | `uint4` | 打包存储的反照率和法线数据 |
| `reflectionPos` | `float3` | 反射位置 |

| 方法 | 说明 |
|------|------|
| `getAlbedo()` / `setAlbedo(float3)` | 获取/设置反照率（R11G11B10 格式打包） |
| `getGuideNormal()` / `setGuideNormal(float3)` | 获取/设置引导法线（2x16bit 编码） |
| `getSpecularAlbedo()` / `setSpecularAlbedo(float3)` | 获取/设置镜面反照率 |
| `getIndirectAlbedo()` / `setIndirectAlbedo(float3)` | 获取/设置间接反照率 |
| `getReflectionPos()` / `setReflectionPos(float3)` | 获取/设置反射位置 |

## 依赖关系 / import

- `Utils.Math.PackedFormats` — 打包格式编解码（R11G11B10 等）
- `Utils.Math.FormatConversion` — 法线编解码
