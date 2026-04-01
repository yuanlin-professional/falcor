# DebugPasses 源码文档

> 路径: `Source/RenderPasses/DebugPasses/DebugPasses.cpp`
> 类型: C++ 实现（插件注册入口）
> 模块: RenderPasses/DebugPasses

## 功能概述

DebugPasses.cpp 是调试渲染通道模块的插件注册入口文件。它将四个调试渲染通道注册到 Falcor 的插件系统中。

## 注册的渲染通道

| 渲染通道 | 说明 |
|----------|------|
| `SplitScreenPass` | 分屏对比通道，支持鼠标交互拖动分割线 |
| `InvalidPixelDetectionPass` | 无效像素检测通道，标记 NaN 和 Inf 像素 |
| `SideBySidePass` | 并排对比通道，将两个输入并排显示 |
| `ColorMapPass` | 颜色映射通道，将单通道数据映射为伪彩色 |

## 依赖关系

### 本文件引用

- `SplitScreenPass/SplitScreenPass.h`
- `InvalidPixelDetectionPass/InvalidPixelDetectionPass.h`
- `SideBySidePass/SideBySidePass.h`
- `ColorMapPass/ColorMapPass.h`
