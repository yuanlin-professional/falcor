# InvalidPixelDetectionPass 源码文档

> 路径: `Source/RenderPasses/DebugPasses/InvalidPixelDetectionPass/InvalidPixelDetectionPass.h` + `InvalidPixelDetectionPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DebugPasses/InvalidPixelDetectionPass

## 功能概述

InvalidPixelDetectionPass 用于检测图像中的无效像素。它将所有包含 NaN 值的像素标记为红色，包含 Inf 值的像素标记为绿色，其他像素显示为黑色。此通道常用于调试渲染管线中的数值问题。

## 类与接口

### `InvalidPixelDetectionPass`

- **继承**: `RenderPass`
- **职责**: 检测并可视化图像中的 NaN 和 Inf 无效像素

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `InvalidPixelDetectionPass(ref<Device>, const Properties&)` | 构造函数，创建全屏检测着色器和帧缓冲区 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入 `src` 和输出 `dst`，使用连接资源的格式和尺寸 |
| `void compile(RenderContext*, const CompileData&)` | 检查反射数据是否就绪 |
| `void execute(RenderContext*, const RenderData&)` | 检查输入格式并执行无效像素检测着色器 |
| `void renderUI(Gui::Widgets&)` | 显示说明文字和输入格式信息 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpInvalidPixelDetectPass` | `ref<FullScreenPass>` | 无效像素检测全屏着色器 |
| `mpFbo` | `ref<Fbo>` | 帧缓冲区 |
| `mFormat` | `ResourceFormat` | 输入纹理格式（用于 UI 显示） |
| `mReady` | `bool` | 反射数据是否就绪 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`
- `Core/Pass/FullScreenPass.h`
- `InvalidPixelDetection.ps.slang`（着色器）

### 被以下文件引用

- `DebugPasses.cpp`（插件注册）

## 实现细节

1. **格式警告**: 当输入纹理不是浮点格式时，会记录警告信息，因为非浮点格式无法表示 Inf/NaN 值。
2. **动态反射**: 使用 `compileData.connectedResources` 获取输入纹理的完整格式信息，确保输出与输入匹配。
