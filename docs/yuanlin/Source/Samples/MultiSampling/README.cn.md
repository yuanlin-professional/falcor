# MultiSampling -- 多重采样示例

## 功能概述

本示例演示了 Falcor 框架中多重采样抗锯齿（MSAA）的使用方法。程序在 CPU 端程序化生成一个由 16 个三角形组成的圆盘几何体，将其渲染到一个 128x128、8 倍采样（8xMSAA）的离屏帧缓冲区中，然后将结果呈现到屏幕。

渲染流程展示了两种 MSAA 解析方式的对比：
- **偶数帧**：先调用 `resolveResource()` 将多重采样纹理显式解析到普通纹理，再 blit 到屏幕。
- **奇数帧**：直接将多重采样纹理 blit 到屏幕（由 blit 操作隐式完成解析）。

着色器（`MultiSampling.3d.slang`）非常简洁，仅执行顶点位置传递和固定灰色输出，重点在于展示 MSAA 帧缓冲区的创建、绑定和解析流程。

## 文件清单

| 文件名 | 类型 | 说明 |
|---|---|---|
| `MultiSampling.cpp` | C++ 源文件 | 应用主逻辑，程序化创建圆盘几何体、配置 8xMSAA FBO、交替使用两种解析方式渲染 |
| `MultiSampling.h` | C++ 头文件 | `MultiSampling` 类声明，包含 RasterPass、VAO、MSAA FBO、解析纹理等成员 |
| `MultiSampling.3d.slang` | Slang 着色器 | 简单的顶点/像素着色器，顶点直传位置，像素输出固定灰色 |
| `CMakeLists.txt` | 构建脚本 | CMake 配置 |

## 依赖关系

- `MultiSampling.cpp` 包含 `MultiSampling.h`，并在运行时加载 `MultiSampling.3d.slang` 着色器。
- `MultiSampling.h` 依赖 `Falcor.h`、`Core/SampleApp` 和 `Core/Pass/RasterPass`。
- 着色器 `MultiSampling.3d.slang` 是独立的，不导入额外的 Slang 模块。
