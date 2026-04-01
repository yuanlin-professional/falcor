# FullScreenPass.vs.slang 源码文档

> 路径: `Source/Falcor/Core/Pass/FullScreenPass.vs.slang`
> 类型: Slang 着色器（顶点着色器）
> 模块: Core/Pass

## 功能概述

全屏渲染通道的默认顶点着色器。将输入的屏幕空间位置和纹理坐标直接传递到后续管线阶段。根据是否启用多视口（`_VIEWPORT_MASK`），输出语义会有所不同——启用多视口时输出 `POSITION` 语义供几何着色器处理，否则输出 `SV_POSITION` 直接用于光栅化。

## 结构体与接口

### `VsOut`

| 成员 | 类型 | 语义 | 说明 |
|------|------|------|------|
| `texC` | `float2` | `TEXCOORD` | 纹理坐标 |
| `posH` | `float4` | `SV_POSITION`（无多视口）或 `POSITION`（有多视口） | 齐次裁剪空间位置 |

## 函数

### `VsOut main(float4 posS: POSITION, float2 texC: TEXCOORD)`

顶点着色器入口函数。直接将输入的屏幕空间位置和纹理坐标赋值到输出结构体，不做任何变换。

**输入参数**:
- `posS` — 屏幕空间顶点位置（来自顶点缓冲区）
- `texC` — 纹理坐标（来自顶点缓冲区）

## 依赖关系

### 预处理宏

| 宏名称 | 说明 |
|--------|------|
| `_VIEWPORT_MASK` | 由 `FullScreenPass.cpp` 在启用多视口时定义，控制输出语义类型 |

### 被以下文件引用

- `FullScreenPass.cpp` — 当程序描述中无顶点着色器入口时，自动添加此着色器
