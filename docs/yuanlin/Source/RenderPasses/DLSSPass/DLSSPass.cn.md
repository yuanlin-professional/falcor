# DLSSPass 源码文档

> 路径: `Source/RenderPasses/DLSSPass/DLSSPass.h` + `DLSSPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DLSSPass

## 功能概述

本文件实现了 NVIDIA DLSS（深度学习超级采样）渲染通道。该通道利用 NVIDIA NGX SDK 提供的深度学习抗锯齿和超分辨率能力，将低分辨率渲染结果上采样为高分辨率输出。支持多种性能/质量配置，包括最大性能、平衡和最高质量三种模式。

通道接受颜色、深度和运动向量作为输入，输出经 DLSS 处理的高分辨率抗锯齿图像。当 DLSS 输出分辨率与通道输出分辨率不匹配时，会自动进行双线性重采样。

## 类与接口

### `DLSSPass`

- **继承**: `RenderPass`
- **职责**: 作为 Falcor 渲染图中的 DLSS 超分辨率/抗锯齿渲染通道

#### 枚举类型

| 枚举 | 值 | 说明 |
|------|------|------|
| `Profile::MaxPerf` | 0 | 最大性能模式 |
| `Profile::Balanced` | 1 | 平衡模式 |
| `Profile::MaxQuality` | 2 | 最高质量模式 |
| `MotionVectorScale::Absolute` | 0 | 运动向量以绝对像素长度提供 |
| `MotionVectorScale::Relative` | 1 | 运动向量以相对屏幕空间长度提供 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `DLSSPass(ref<Device>, const Properties&)` | 构造函数，解析属性并创建曝光纹理 |
| `Properties getProperties() const` | 序列化通道属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（color、depth、mvec）和输出（RGBA32Float） |
| `void execute(RenderContext*, const RenderData&)` | 执行 DLSS 处理 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI：启用开关、输出尺寸、Profile、运动向量缩放、HDR、锐化、曝光 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景引用 |
| `void initializeDLSS(RenderContext*)` | 查询最优设置并初始化 DLSS 功能 |
| `void executeInternal(RenderContext*, const RenderData&)` | 内部执行逻辑：验证输入、计算抖动偏移、调用 NGX 评估 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEnabled` | `bool` | DLSS 启用开关 |
| `mProfile` | `Profile` | 当前性能/质量配置 |
| `mMotionVectorScale` | `MotionVectorScale` | 运动向量缩放模式 |
| `mIsHDR` | `bool` | 输入是否为 HDR 内容 |
| `mSharpness` | `float` | 锐化值 [-1, 1] |
| `mExposure` | `float` | 曝光值 |
| `mInputSize` | `uint2` | 输入分辨率（像素） |
| `mDLSSOutputSize` | `uint2` | DLSS 输出分辨率 |
| `mPassOutputSize` | `uint2` | 通道输出分辨率（可能与 DLSS 不同） |
| `mpNGXWrapper` | `unique_ptr<NGXWrapper>` | NGX 功能包装器 |
| `mpOutput` | `ref<Texture>` | 内部输出缓冲区 |
| `mpExposure` | `ref<Texture>` | 1x1 曝光纹理 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心框架
- `RenderGraph/RenderPassHelpers.h` — 渲染通道辅助工具
- `NGXWrapper.h` — NGX SDK 封装

### 被以下文件引用

- 渲染图配置脚本通过 `DLSSPass` 名称引用

## 实现细节

1. **抖动偏移处理**: DLSS 要求 X 抖动从左到右、Y 抖动从上到下。Falcor 使用 `math::perspective()` 产生 Y 从下到上的坐标系，因此需要翻转 Y 抖动。

2. **输出尺寸管理**: 支持 Default（由连接的通道决定）和 Fixed（固定为 DLSS 最优输出尺寸）两种模式。当 Fixed 模式下尺寸不匹配时触发图重编译。

3. **内部缓冲区**: 当通道输出尺寸或格式与 DLSS 输出不同时，先写入内部缓冲区 `mpOutput`，再通过 blit 复制到通道输出。

4. **禁用时行为**: 当 DLSS 被禁用或无场景时，直接将输入颜色 blit 到输出。
