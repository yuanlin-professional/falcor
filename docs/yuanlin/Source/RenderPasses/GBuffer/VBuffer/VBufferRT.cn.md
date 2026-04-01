# VBufferRT 源码文档

> 路径: `Source/RenderPasses/GBuffer/VBuffer/VBufferRT.h` + `Source/RenderPasses/GBuffer/VBuffer/VBufferRT.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/GBuffer/VBuffer

## 功能概述

基于光线追踪的 V-buffer（可见性缓冲区）生成渲染通道。V-buffer 存储打包的网格实例 ID、图元索引和重心坐标，后续通道可以从中重建完整的几何和材质信息。支持传统光线追踪和内联光线追踪两种模式，以及景深计算。

## 类与接口

### `VBufferRT`

- **继承**: `GBufferBase`
- **职责**: 使用光线追踪生成 V-buffer 和辅助输出。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `reflect(const CompileData&)` | 声明 V-buffer 主输出和额外通道 |
| `execute(...)` | 执行光线追踪或计算通道 |
| `executeRaytrace(...)` | TraceRay 模式执行 |
| `executeCompute(...)` | 内联光线追踪模式执行 |

#### 额外输出通道

| 通道名 | 格式 | 说明 |
|--------|------|------|
| `depth` | R32Float | NDC 深度 |
| `mvec` | RG32Float | 运动矢量 |
| `viewW` | RGBA32Float | 视线方向 |
| `time` | R32Uint | 每像素执行时间 |
| `mask` | R32Float | 掩码 |

## 依赖关系

### 本文件引用

- `GBufferBase.h` — 基类
- `VBufferRT.rt.slang`, `VBufferRT.cs.slang` — 着色器文件

## 实现细节

- 需要 Shader Model 6.5 和 Raytracing Tier 1.1
- 通过场景更新标志检测是否需要重建着色器程序
- 光线标志支持前面/背面剔除
