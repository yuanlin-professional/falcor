# SVGFPass 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFPass.h` + `Source/RenderPasses/SVGFPass/SVGFPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/SVGFPass

## 功能概述

实现 SVGF（Spatiotemporal Variance-Guided Filtering）降噪渲染通道。SVGF 是一种时空方差引导的滤波算法，通过时间重投影、方差估计和 A-trous 小波分解实现实时降噪。处理流程为：打包线性 Z 和法线 → 时间重投影 → 滤波矩（方差估计）→ A-trous 分解 → 最终调制（反照率 * 滤波光照 + 发射）。

## 类与接口

### `SVGFPass`

- **继承**: `RenderPass`
- **职责**: 管理 SVGF 降噪管线的完整执行流程。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `reflect(const CompileData&)` | 声明输入（反照率、颜色、发射、世界位置、法线、位置法线 Fwidth、线性 Z、运动矢量）和输出（滤波图像） |
| `compile(RenderContext*, const CompileData&)` | 分配 FBO 资源 |
| `execute(RenderContext*, const RenderData&)` | 依次执行降噪管线的各阶段 |
| `computeLinearZAndNormal(...)` | 打包线性 Z 和法线到单一缓冲区 |
| `computeReprojection(...)` | 时间重投影，融合前一帧滤波光照 |
| `computeFilteredMoments(...)` | 滤波矩以估计方差（历史长度不足时空间滤波） |
| `computeAtrousDecomposition(...)` | A-trous 小波分解多次迭代滤波 |
| `allocateFbos(uint2, RenderContext*)` | 分配中间帧缓冲区 |
| `clearBuffers(...)` | 清空所有中间缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFilterEnabled` | `bool` | 是否启用滤波 |
| `mFilterIterations` | `int32_t` | A-trous 滤波迭代次数（默认 4） |
| `mFeedbackTap` | `int32_t` | 反馈到下一帧的迭代索引 |
| `mPhiColor` | `float` | 颜色边缘停止参数 |
| `mPhiNormal` | `float` | 法线边缘停止参数 |
| `mAlpha` | `float` | 时间混合因子（0=完全复用，1=不复用） |
| `mMomentsAlpha` | `float` | 矩的时间混合因子 |
| `mpPackLinearZAndNormal` | `ref<FullScreenPass>` | 打包线性 Z 和法线的全屏通道 |
| `mpReprojection` | `ref<FullScreenPass>` | 重投影通道 |
| `mpFilterMoments` | `ref<FullScreenPass>` | 滤波矩通道 |
| `mpAtrous` | `ref<FullScreenPass>` | A-trous 滤波通道 |
| `mpFinalModulate` | `ref<FullScreenPass>` | 最终调制通道 |
| `mpPingPongFbo[2]` | `ref<Fbo>` | 乒乓缓冲区 |
| `mpCurReprojFbo` / `mpPrevReprojFbo` | `ref<Fbo>` | 当前/前一帧重投影缓冲区 |

## 依赖关系

### 本文件引用

- `Falcor.h`, `RenderGraph/RenderPass.h`, `Core/Pass/FullScreenPass.h`
- SVGF 着色器文件（SVGFPackLinearZAndNormal、SVGFReproject、SVGFAtrous、SVGFFilterMoments、SVGFFinalModulate）

## 实现细节

- 使用乒乓缓冲区策略执行多次 A-trous 迭代
- 重投影使用双线性插值和跨双边滤波进行验证
- 中间缓冲区使用 3 个 MRT：RGBA32F（光照）、RG32F（矩）、R16F（历史长度）
- 输入颜色先解调反照率得到光照，最终再乘以反照率恢复
