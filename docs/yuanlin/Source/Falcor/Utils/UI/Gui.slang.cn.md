# Gui.slang 源码文档

> 路径: `Source/Falcor/Utils/UI/Gui.slang`
> 类型: Slang 着色器
> 模块: Utils/UI

## 功能概述

Gui.slang 是 Falcor GUI 系统（基于 ImGui）的渲染着色器。它负责将 ImGui 生成的顶点数据变换到屏幕空间并进行纹理采样，支持字体纹理渲染和普通图片渲染两种模式。

## 结构体与接口

### `VsIn`

顶点着色器输入结构体。

| 成员 | 语义 | 类型 | 说明 |
|------|------|------|------|
| `pos` | `POSITION` | `float2` | 屏幕空间顶点位置 |
| `texC` | `TEXCOORD0` | `float2` | 纹理坐标 |
| `color` | `COLOR` | `float4` | 顶点颜色 |

### `VsOut`

顶点着色器输出结构体。

| 成员 | 语义 | 类型 | 说明 |
|------|------|------|------|
| `color` | `COLOR` | `float4` | 传递的顶点颜色 |
| `texC` | `TEXCOORD0` | `float2` | 传递的纹理坐标 |
| `pos` | `SV_POSITION` | `float4` | 裁剪空间位置 |

### `PerFrameCB`

逐帧常量缓冲区。

| 成员 | 类型 | 说明 |
|------|------|------|
| `scale` | `float2` | 位置缩放因子（用于视口变换） |
| `offset` | `float2` | 位置偏移量（用于视口变换） |
| `useGuiImage` | `bool` | 是否使用 GUI 图片模式（而非字体模式） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `VsOut vsMain(VsIn vIn)` | 顶点着色器：将顶点位置通过 `scale` 和 `offset` 变换到裁剪空间，传递颜色和纹理坐标 |
| `float4 psMain(VsOut vOut) : SV_TARGET` | 像素着色器：根据 `useGuiImage` 标志选择渲染模式 -- 字体模式下从字体纹理采样 alpha 并与顶点颜色相乘；图片模式下直接采样 GUI 图片纹理 |

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gSampler` | `SamplerState` | 纹理采样器 |
| `gFont` | `Texture2D` | 字体纹理图集 |
| `guiImage` | `Texture2D` | GUI 图片纹理（用于 ImGui 的 Image 控件） |

## 依赖关系

### import

无外部 import。

### 被以下文件引用

- `Utils/UI/Gui.cpp` — 作为 GUI 渲染管线的着色器程序加载

## 实现细节

- 顶点变换使用简单的缩放+偏移公式 `pos * scale + offset`，而非完整的矩阵变换，因为 ImGui 顶点已在屏幕空间中
- Z 值固定为 0，W 值固定为 1，不参与深度测试
- 像素着色器通过 `useGuiImage` 在两种模式间切换：
  - **字体模式**: 从 `gFont` 采样红色通道作为 alpha 遮罩，与顶点颜色相乘
  - **图片模式**: 直接从 `guiImage` 采样完整 RGBA 颜色
