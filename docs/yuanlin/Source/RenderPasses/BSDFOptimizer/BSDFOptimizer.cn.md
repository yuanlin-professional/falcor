# BSDFOptimizer 源码文档

> 路径: `Source/RenderPasses/BSDFOptimizer/BSDFOptimizer.h` + `Source/RenderPasses/BSDFOptimizer/BSDFOptimizer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/BSDFOptimizer

## 功能概述

BSDFOptimizer 是一个使用可微材质进行双向散射分布函数参数优化的渲染通道。它通过比较初始材质和参考材质的双向散射分布函数切片，使用 Adam 优化器迭代调整材质参数，最小化两者之间的 L2 损失。可视化界面展示三视图：初始材质、绝对差异和参考材质。

## 类与接口

### `BSDFOptimizer`

- **继承**: `RenderPass`
- **职责**: 使用可微渲染优化双向散射分布函数参数以匹配参考材质

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BSDFOptimizer(ref<Device>, const Properties&)` | 构造函数，创建采样生成器和 GPU 围栏 |
| `Properties getProperties() const override` | 序列化初始和参考材质 ID |
| `RenderPassReflection reflect(const CompileData&) override` | 声明 `output` 缓冲区 |
| `void compile(RenderContext*, const CompileData&) override` | 计算三视口布局（左/中/右） |
| `void execute(RenderContext*, const RenderData&) override` | 执行优化步骤和可视化 |
| `void renderUI(Gui::Widgets&) override` | 渲染 UI：开始/停止/重置优化，材质选择和属性显示 |
| `void setScene(RenderContext*, const ref<Scene>&) override` | 加载场景，创建优化器和查看器通道，初始化梯度存储 |
| `void registerBindings(pybind11::module&)` | Python 绑定：init_material_id、ref_material_id、bsdf_slice_resolution、compute_bsdf_grads |
| `void initOptimization()` | 重置材质参数和 Adam 优化器 |
| `void executeOptimizerPass(RenderContext*)` | 执行梯度计算通道 |
| `void step(RenderContext*)` | 回读梯度并执行 Adam 优化步骤 |
| `ref<Buffer> computeBSDFGrads()` | Python 接口：计算并返回梯度缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 已加载场景 |
| `mpSceneGradients` | `unique_ptr<SceneGradients>` | 场景梯度存储 |
| `mInitBSDFParams` | `SerializedMaterialParams` | 初始材质序列化参数 |
| `mRefBSDFParams` | `SerializedMaterialParams` | 参考材质序列化参数 |
| `mCurBSDFParams` | `SerializedMaterialParams` | 当前优化中的材质参数 |
| `mBSDFGrads` | `SerializedMaterialParams` | 梯度值 |
| `mAdam` | `AdamOptimizer` | Adam 优化器实例 |
| `mParams` | `BSDFOptimizerParams` | 共享参数 |
| `mpOptimizerPass` | `ref<ComputePass>` | 梯度计算通道 |
| `mpViewerPass` | `ref<ComputePass>` | 可视化通道 |

### `AdamOptimizer`（内部结构）

| 成员 | 类型 | 说明 |
|------|------|------|
| `lr` | `vector<float>` | 每参数学习率 |
| `beta1` | `float` | 一阶矩衰减率，默认 0.9 |
| `beta2` | `float` | 二阶矩衰减率，默认 0.999 |
| `epsilon` | `float` | 数值稳定项，默认 1e-6 |
| `steps` | `int` | 已执行步数 |
| `m`, `v` | `vector<float>` | 一阶和二阶矩估计 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `Utils/Sampling/SampleGenerator.h` — 采样生成器
- `DiffRendering/SceneGradients.h` — 场景梯度
- `BSDFOptimizerParams.slang` — 共享参数
- `<fstd/span.h>` — span 工具

### 被以下文件引用

- `BSDFOptimizer.cs.slang` — 梯度计算着色器
- `BSDFViewer.cs.slang`（BSDFOptimizer 目录下） — 可视化着色器

## 实现细节

- **预配置学习率**: 为不同材质类型（PBRTDiffuse、PBRTConductor、Standard）预设各参数的学习率。未配置的参数学习率为 0，不参与优化。
- **Adam 优化器**: 完整实现 Adam 算法，包含偏差校正的一阶和二阶矩估计。
- **自动停止**: 当相对 L1 误差低于 1e-3 时自动停止优化。
- **梯度计算**: 通过 `SceneGradients` 管理 GPU 端梯度的清零、累积和聚合。
- **三视口布局**: 将帧宽度三等分，分别显示初始材质、绝对差异和参考材质的双向散射分布函数切片。
- **Python 接口**: 支持通过脚本设置切片分辨率和直接计算梯度，用于自动化测试和研究。
