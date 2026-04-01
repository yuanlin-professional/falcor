# ErrorMeasurePass 源码文档

> 路径: `Source/RenderPasses/ErrorMeasurePass/ErrorMeasurePass.h` + `ErrorMeasurePass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/ErrorMeasurePass

## 功能概述

误差度量渲染通道，用于计算源图像与参考图像之间的 L1 或 L2（MSE）误差。支持从文件加载参考图像或使用输入通道，可选忽略背景像素、计算 RGB 平均误差、指数滑动平均误差报告，以及将测量结果导出为 CSV 文件。

## 类与接口

### `ErrorMeasurePass`

- **继承**: `RenderPass`
- **职责**: 计算源图像与参考图像的逐像素差异，执行并行归约求平均误差

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ErrorMeasurePass(ref<Device>, const Properties&)` | 构造函数，解析属性，加载参考图像和测量文件 |
| `Properties getProperties() const` | 序列化属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（Source、Reference 可选、WorldPosition 可选）和输出（Output） |
| `void execute(RenderContext*, const RenderData&)` | 执行差异计算和并行归约 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI（加载参考、设置输出文件、显示误差） |
| `bool onKeyEvent(const KeyboardEvent&)` | 按 O 键切换输出模式 |
| `void runDifferencePass(RenderContext*, const RenderData&)` | 运行差异计算着色器 |
| `void runReductionPasses(RenderContext*, const RenderData&)` | 运行并行归约计算平均误差 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpErrorMeasurerPass` | `ref<ComputePass>` | 差异计算着色器 |
| `mpParallelReduction` | `unique_ptr<ParallelReduction>` | 并行归约工具 |
| `mMeasurements` | 匿名结构体 | 当前帧的误差数据 |
| `mRunningError` | `float3` | 指数滑动平均 RGB 误差 |
| `mRunningAvgError` | `float` | 指数滑动平均误差 |
| `mSelectedOutputId` | `OutputId` | 当前输出选择（Source/Reference/Difference） |
| `mIgnoreBackground` | `bool` | 是否忽略背景像素 |
| `mComputeSquaredDifference` | `bool` | 是否计算平方差（L2 vs L1） |
| `mComputeAverage` | `bool` | 是否计算 RGB 平均 |

### `OutputId` (枚举)

输出选择：`Source`、`Reference`、`Difference`。

## 依赖关系

### 本文件引用

- `Falcor.h`、`RenderGraph/RenderPass.h`
- `Utils/Algorithm/ParallelReduction.h` — 并行归约
- `Core/AssetResolver.h` — 资源路径解析
- `ErrorMeasurer.cs.slang` — 差异计算着色器

### 被以下文件引用

- 渲染图系统通过插件注册机制加载

## 实现细节

- 差异纹理在源图像分辨率变化时重新创建（RGBA32Float）
- 并行归约使用 `ParallelReduction::Type::Sum` 计算总误差，然后除以像素数
- 指数滑动平均公式：`running = sigma * running + (1 - sigma) * current`
- 测量结果可输出到 CSV 文件，包含 avg/red/green/blue 误差列
