# TextRenderer.3d.slang 源码文档

> 路径: `Source/Falcor/Utils/UI/TextRenderer.3d.slang`
> 类型: Slang 着色器
> 模块: Utils/UI

## 功能概述

TextRenderer.3d.slang 是文本渲染器（TextRenderer）使用的着色器程序。它将屏幕空间的字符四边形通过变换矩阵转换到裁剪空间，并从字体纹理中采样获取字符的 Alpha 遮罩，结合指定的字体颜色输出最终像素颜色。

## 结构体与接口

### `PerFrameCB`

逐帧常量缓冲区。

| 成员 | 类型 | 说明 |
|------|------|------|
| `gvpTransform` | `float4x4` | 视口变换矩阵，将像素坐标转换为裁剪空间坐标 |
| `gFontColor` | `float3` | 字体颜色（RGB） |

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gFontTex` | `Texture2D` | 字体纹理图集 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 transform(float2 posS)` | 辅助函数：将屏幕空间 2D 位置通过 `gvpTransform` 变换为裁剪空间 4D 坐标（Z = 0.5） |
| `void vsMain(float2 posS: POSITION, inout float2 texC: TEXCOORD, out float4 posSV: SV_POSITION)` | 顶点着色器：变换顶点位置，直接传递纹理坐标 |
| `float4 calcColor(float2 texC)` | 辅助函数：从字体纹理的整数坐标采样颜色，将 RGB 替换为 `gFontColor`，保留 Alpha |
| `float4 psMain(float2 texC: TEXCOORD) : SV_TARGET0` | 像素着色器：调用 `calcColor` 输出最终颜色 |

## 依赖关系

### import

无外部 import。

### 被以下文件引用

- `Utils/UI/TextRenderer.cpp` — 通过 `RasterPass::create` 加载此着色器

## 实现细节

- 与 Gui.slang 不同，此着色器使用完整的 4x4 变换矩阵而非简单的缩放+偏移，因为 TextRenderer 需要处理像素坐标到 NDC 的变换
- 纹理采样使用 `Load`（整数坐标直接读取）而非 `Sample`，因为纹理坐标已经是像素坐标而非归一化坐标
- 字体纹理的 RGBA 通道中，Alpha 包含字符形状信息，RGB 被替换为统一的字体颜色
- Z 值固定为 0.5，处于深度范围中间（深度测试已在 C++ 端禁用）
- 顶点着色器使用 `inout` 语义直接传递纹理坐标，无需额外的输出结构体
