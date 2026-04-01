# VBufferRaster 源码文档

> 路径: `Source/RenderPasses/GBuffer/VBuffer/VBufferRaster.h` + `Source/RenderPasses/GBuffer/VBuffer/VBufferRaster.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/GBuffer/VBuffer

## 功能概述

基于光栅化的 V-buffer 生成渲染通道。使用单遍光栅化写入可见性缓冲区，同时可输出运动矢量和掩码等辅助通道。V-buffer 作为渲染目标输出，辅助通道通过 ROV 作为 UAV 输出。

## 类与接口

### `VBufferRaster`

- **继承**: `GBufferBase`
- **职责**: 使用光栅化管线生成 V-buffer。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `reflect(const CompileData&)` | 声明深度和 V-buffer 输出 |
| `execute(...)` | 清除缓冲区并执行光栅化 |
| `setScene(...)` | 验证三角形列表拓扑 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpFbo` | `ref<Fbo>` | 帧缓冲区 |
| `mRaster` | 结构体 | 光栅化状态、程序和变量 |

## 依赖关系

### 本文件引用

- `GBufferBase.h` — 基类
- `VBufferRaster.3d.slang` — 光栅化着色器

## 实现细节

- 需要 Shader Model 6.2、重心坐标和 ROV 支持
- 使用 LessEqual 深度函数
- 仅支持三角形列表拓扑
