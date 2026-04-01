# ColorMapPass 源码文档

> 路径: `Source/RenderPasses/DebugPasses/ColorMapPass/ColorMapPass.h` + `ColorMapPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DebugPasses/ColorMapPass

## 功能概述

ColorMapPass 将输入图像的指定通道值映射为伪彩色输出。支持多种颜色映射方案（Grey、Jet、Viridis、Plasma、Magma、Inferno），可选择输入通道，支持手动设置值范围或自动范围检测。常用于可视化深度图、热力图等单通道数据。

## 类与接口

### `ColorMapPass`

- **继承**: `RenderPass`
- **职责**: 将单通道数据映射为伪彩色可视化

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ColorMapPass(ref<Device>, const Properties&)` | 构造函数，解析颜色映射、通道、范围等属性 |
| `Properties getProperties() const` | 序列化所有属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入 `input` 和输出 `output` |
| `void execute(RenderContext*, const RenderData&)` | 执行自动范围检测（如启用）和颜色映射着色器 |
| `void renderUI(Gui::Widgets&)` | 颜色映射下拉框、通道选择、自动范围开关、最小/最大值 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mColorMap` | `ColorMap` | 颜色映射方案（枚举） |
| `mChannel` | `uint32_t` | 读取的输入通道索引（0-3） |
| `mAutoRange` | `bool` | 是否启用自动范围检测 |
| `mMinValue` / `mMaxValue` | `float` | 值范围的最小/最大值 |
| `mpColorMapPass` | `ref<FullScreenPass>` | 颜色映射全屏着色器 |
| `mpAutoRanging` | `unique_ptr<AutoRanging>` | 自动范围检测器 |

### `ColorMapPass::AutoRanging`（内部类）

- **职责**: 使用并行归约异步计算纹理的最小/最大值

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AutoRanging(ref<Device>)` | 构造，创建并行归约器、结果缓冲区和栅栏 |
| `optional<pair<double,double>> getMinMax(RenderContext*, ref<Texture>&, uint32_t)` | 异步获取指定通道的最小/最大值 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `Core/Pass/FullScreenPass.h`
- `RenderGraph/RenderPass.h`
- `Utils/Algorithm/ParallelReduction.h`
- `ColorMapParams.slang`
- `ColorMapPass.ps.slang`（着色器）

### 被以下文件引用

- `DebugPasses.cpp`（插件注册）

## 实现细节

1. **自动范围**: 使用 `ParallelReduction` 异步计算纹理的最小/最大值。立即扩大范围以包含新值，使用指数平滑（alpha=0.01）缓慢缩小范围。
2. **格式支持**: 支持 Float、Uint、Sint 三种纹理格式，通过宏定义在着色器编译时选择。
3. **异步读回**: 使用 GPU 栅栏（Fence）和 ReadBack 缓冲区实现异步结果读取，避免 GPU 停顿。
