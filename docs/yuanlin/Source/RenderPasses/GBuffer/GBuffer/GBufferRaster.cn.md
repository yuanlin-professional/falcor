# GBufferRaster 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferRaster.h` + `Source/RenderPasses/GBuffer/GBuffer/GBufferRaster.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/GBuffer

## 功能概述

基于光栅化的 G-buffer 生成渲染通道。使用两遍渲染策略：第一遍为深度预通道（Depth Pass），第二遍在等深度条件下渲染完整 G-buffer 数据。标准 G-buffer 通道作为渲染目标输出，额外通道通过 ROV（光栅化有序视图）作为 UAV 输出。

## 类与接口

### `GBufferRaster`

- **继承**: `GBuffer`
- **职责**: 使用光栅化管线生成 G-buffer 数据。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `reflect(const CompileData&)` | 声明深度输出（D32Float）、标准 G-buffer（RenderTarget）和额外通道（UAV） |
| `execute(RenderContext*, const RenderData&)` | 执行深度预通道和 G-buffer 渲染通道 |
| `setScene(...)` | 设置场景，验证三角形列表拓扑 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpFbo` | `ref<Fbo>` | 帧缓冲区对象 |
| `mDepthPass` | 结构体 | 深度预通道状态、程序和变量 |
| `mGBufferPass` | 结构体 | G-buffer 通道状态、程序和变量 |

## 依赖关系

### 本文件引用

- `GBuffer.h` — 基类
- `DepthPass.3d.slang` — 深度预通道着色器
- `GBufferRaster.3d.slang` — G-buffer 光栅化着色器

## 实现细节

- 需要 Shader Model 6.2、像素着色器重心坐标和 ROV 支持
- 深度预通道使用 LessEqual 深度函数，G-buffer 通道使用 Equal 深度函数
- 仅支持三角形列表拓扑（因使用 SV_Barycentrics）
- 默认剔除模式为背面剔除
