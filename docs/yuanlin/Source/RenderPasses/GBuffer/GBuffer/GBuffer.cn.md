# GBuffer 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBuffer.h` + `Source/RenderPasses/GBuffer/GBuffer/GBuffer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/GBuffer

## 功能概述

G-buffer 渲染通道的中间基类，为光栅化和光线追踪两种 G-buffer 实现提供共享的通道定义。定义了 8 个标准 G-buffer 通道（对应 8 个渲染目标）的通道列表。

## 类与接口

### `GBuffer`

- **继承**: `GBufferBase`
- **职责**: 定义标准 G-buffer 通道列表，作为 GBufferRaster 和 GBufferRT 的直接基类。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `kGBufferChannels` | `ChannelList`（静态常量） | 8 个标准 G-buffer 通道定义 |

### 标准 G-buffer 通道

| 通道名 | 着色器名 | 格式 | 说明 |
|--------|----------|------|------|
| `posW` | `gPosW` | RGBA32Float | 世界空间位置 |
| `normW` | `gNormW` | RGBA32Float | 世界空间着色法线 |
| `tangentW` | `gTangentW` | RGBA32Float | 世界空间切线和符号 |
| `faceNormalW` | `gFaceNormalW` | RGBA32Float | 世界空间面法线 |
| `texC` | `gTexC` | RG32Float | 纹理坐标 |
| `texGrads` | `gTexGrads` | RGBA16Float | 纹理梯度 |
| `mvec` | `gMotionVector` | RG32Float | 运动矢量 |
| `mtlData` | `gMaterialData` | RGBA32Uint | 材质数据（ID、头部、瓣类型） |

## 依赖关系

### 本文件引用

- `GBufferBase.h` — 基类

### 被以下文件引用

- `GBufferRT.h`, `GBufferRaster.h` — 具体实现类
