# FLIPPass 源码文档

> 路径: `Source/RenderPasses/FLIPPass/FLIPPass.h` + `FLIPPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/FLIPPass

## 功能概述

FLIP 图像质量度量渲染通道，实现了 LDR-FLIP 和 HDR-FLIP 算法，用于可视化和量化测试图像与参考图像之间的感知差异。支持 Magma 颜色映射、多种色调映射器（ACES/Hable/Reinhard）、自定义曝光参数，以及通过并行归约计算全帧 FLIP 均值/最小值/最大值。

## 类与接口

### `FLIPPass`

- **继承**: `RenderPass`
- **职责**: 计算并显示 FLIP 误差图

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `FLIPPass(ref<Device>, const Properties&)` | 构造函数，初始化着色器通道和监视器信息 |
| `Properties getProperties() const` | 序列化属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（testImage、referenceImage）和输出（errorMap、errorMapDisplay、exposureMapDisplay） |
| `void execute(RenderContext*, const RenderData&)` | 执行 FLIP 计算、亮度计算、曝光参数推导和并行归约 |
| `void renderUI(Gui::Widgets&)` | 渲染设置 UI（HDR、色调映射器、曝光、监视器参数） |
| `void updatePrograms()` | 更新着色器定义（色调映射器选择） |
| `void computeExposureParameters(float, float)` | 从参考图像亮度统计推导 HDR-FLIP 曝光参数 |
| `void parseProperties(const Properties&)` | 解析属性字典 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEnabled` | `bool` | 启用 FLIP 计算 |
| `mIsHDR` | `bool` | 是否计算 HDR-FLIP |
| `mToneMapper` | `FLIPToneMapperType` | 色调映射器类型 |
| `mStartExposure` / `mStopExposure` | `float` | HDR-FLIP 曝光范围 |
| `mNumExposures` | `uint32_t` | 曝光数量 |
| `mMonitorWidthPixels` | `uint` | 监视器水平分辨率 |
| `mMonitorWidthMeters` | `float` | 监视器物理宽度（米） |
| `mMonitorDistanceMeters` | `float` | 观看距离（米） |
| `mpFLIPPass` | `ref<ComputePass>` | FLIP 计算着色器 |
| `mpComputeLuminancePass` | `ref<ComputePass>` | 亮度计算着色器 |
| `mpParallelReduction` | `unique_ptr<ParallelReduction>` | 并行归约 |
| `mComputePooledFLIPValues` | `bool` | 是否计算全帧统计 |
| `mAverageFLIP` / `mMinFLIP` / `mMaxFLIP` | `float` | FLIP 统计值 |

## 依赖关系

### 本文件引用

- `Falcor.h`、`RenderGraph/RenderPass.h`
- `Core/Platform/MonitorInfo.h` — 监视器信息
- `Utils/Algorithm/ParallelReduction.h` — 并行归约
- `ToneMappers.slang` — 色调映射器枚举
- `FLIPPass.cs.slang` — FLIP 计算着色器
- `ComputeLuminance.cs.slang` — 亮度计算着色器

### 被以下文件引用

- 渲染图系统通过插件注册机制加载

## 实现细节

- HDR-FLIP 通过在多个曝光级别上运行 LDR-FLIP 并取最大值实现
- 曝光参数自动推导：从参考图像亮度的中位数和最大值，通过色调映射器特征方程求解
- 显示输出需要 sRGB->Linear 预补偿，因为后续 blit 会执行 Linear->sRGB 变换
- 并行归约使用 Sum 和 MinMax 两种模式分别计算均值和极值
- 支持从操作系统获取真实监视器参数
