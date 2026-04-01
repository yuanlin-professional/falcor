# Falcor.h 源码文档

> 路径: `Source/Falcor/Falcor.h`
> 类型: C++ 头文件（预编译头/全局包含）
> 模块: Falcor 根模块

## 功能概述

`Falcor.h` 是 Falcor 渲染框架的全局头文件，汇集了框架所有核心子系统的头文件引用。它作为预编译头或便捷的统一包含文件使用，使得下游代码只需 `#include "Falcor.h"` 即可访问框架的全部公共 API。

## 引用的子系统

### Core

| 头文件 | 说明 |
|--------|------|
| `Core/Macros.h` | 宏定义 |
| `Core/Version.h` | 版本信息 |
| `Core/Error.h` | 错误处理 |

### Core/Platform

| 头文件 | 说明 |
|--------|------|
| `Core/Platform/OS.h` | 操作系统抽象层 |
| `Core/Platform/ProgressBar.h` | 进度条 |

### Core/API

| 头文件 | 说明 |
|--------|------|
| `Core/API/Types.h` | 基础类型 |
| `Core/API/BlendState.h` | 混合状态 |
| `Core/API/Buffer.h` | 缓冲区 |
| `Core/API/ComputeContext.h` | 计算上下文 |
| `Core/API/Device.h` | GPU 设备 |
| `Core/API/FBO.h` | 帧缓冲区对象 |
| `Core/API/Formats.h` | 资源格式 |
| `Core/API/Texture.h` | 纹理 |
| `Core/API/Sampler.h` | 采样器 |
| `Core/API/RenderContext.h` | 渲染上下文 |
| 其他 | 混合状态、光栅化状态、深度模板状态、光线追踪状态对象等 |

### Core/Program

| 头文件 | 说明 |
|--------|------|
| `Core/Program/Program.h` | 着色器程序 |
| `Core/Program/ProgramReflection.h` | 程序反射 |
| `Core/Program/ProgramVars.h` | 程序变量 |
| `Core/Program/ProgramVersion.h` | 程序版本 |

### Core/State

| 头文件 | 说明 |
|--------|------|
| `Core/State/ComputeState.h` | 计算状态 |
| `Core/State/GraphicsState.h` | 图形状态 |

### Scene

| 头文件 | 说明 |
|--------|------|
| `Scene/Scene.h` | 场景 |
| `Scene/Camera/Camera.h` | 相机 |
| `Scene/Lights/Light.h` | 光源 |
| `Scene/Material/MaterialSystem.h` | 材质系统 |
| `Scene/Animation/Animation.h` | 动画 |
| 其他 | 相机控制器、标准/头发/布料材质等 |

### Utils

| 头文件 | 说明 |
|--------|------|
| `Utils/Math/Common.h` | 通用数学 |
| `Utils/Math/Vector.h` | 向量 |
| `Utils/Logger.h` | 日志 |
| `Utils/UI/InputTypes.h` | 输入类型 |
| `Utils/Timing/Profiler.h` | 性能分析 |
| `Utils/StringFormatters.h` | 字符串格式化 |

### 标准库

包含 `<memory>`, `<iostream>`, `<string>`, `<vector>`, `<algorithm>`, `<filesystem>`, `<variant>`, `<cstdint>`, `<cmath>` 等标准头文件，以及 `fmt/format.h` 和 `fstd/span.h` 等第三方库。

## 依赖关系

### 被以下文件引用

- Falcor 框架中几乎所有 C++ 源文件均可能直接或间接包含此头文件
