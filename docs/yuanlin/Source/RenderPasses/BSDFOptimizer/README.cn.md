# BSDFOptimizer -- BSDF 优化器

## 功能概述

BSDFOptimizer 是一个利用可微材质进行 BSDF 参数优化的渲染通道插件。该通道通过计算 BSDF 切片上的梯度，使用 Adam 优化器自动拟合初始材质参数到目标参考材质，适用于材质参数逆向求解和材质匹配。

核心特性：
- 基于可微渲染的 BSDF 参数优化：利用 Falcor 的 `SceneGradients` 系统计算材质梯度
- 内置 Adam 优化器：支持每个参数独立学习率，自适应矩估计
- 三视口对比展示：左侧为当前优化材质、中间为绝对差异、右侧为参考材质
- 针对不同材质类型预设学习率：支持 `PBRTDiffuse`、`PBRTConductor`、`Standard` 材质
- 自动收敛检测：当相对 L1 误差小于阈值时自动停止
- 通过 Python 绑定支持外部脚本控制优化流程
- 可配置 BSDF 切片分辨率

## 架构图

```mermaid
flowchart TD
    A[RenderGraph 调度] --> B[BSDFOptimizer::execute]
    B --> C{正在优化?}
    C -- 是 --> D[executeOptimizerPass<br>计算梯度]
    D --> E[clearGrads 清空梯度缓冲]
    E --> F[ComputePass::execute<br>BSDFOptimizer.cs.slang]
    F --> G[aggregateGrads<br>聚合梯度]
    G --> H[step 优化步骤]
    H --> I[从 GPU 读回梯度]
    I --> J[Adam 优化器更新参数]
    J --> K[deserializeParams<br>写回材质参数]
    K --> L{收敛?}
    L -- 是 --> M[停止优化]
    L -- 否 --> N[继续下一帧]
    C -- 否 --> N

    N --> O[executeViewerPass<br>渲染可视化]
    O --> P[ComputePass::execute<br>BSDFViewer.cs.slang]
    P --> Q[三视口并排渲染<br>初始 | 差异 | 参考]
    Q --> R[输出到 output 纹理]

    subgraph Adam 优化器
        AD1[计算一阶矩 m]
        AD2[计算二阶矩 v]
        AD3[偏差修正 mHat, vHat]
        AD4[更新参数 x -= lr * mHat / sqrt vHat + eps]
        AD1 --> AD2 --> AD3 --> AD4
    end

    subgraph GPU 梯度计算
        GP1[SceneGradients 管理]
        GP2[可微材质求值<br>setupDiffMaterialInstance]
        GP3[BSDF 切片上求值]
        GP4[反向传播计算梯度]
        GP1 --> GP2 --> GP3 --> GP4
    end
```

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `BSDFOptimizer.h` | C++ 头文件 | `BSDFOptimizer` 类声明，包含 `AdamOptimizer` 内部结构体 |
| `BSDFOptimizer.cpp` | C++ 源文件 | 渲染通道主逻辑：优化循环、Adam 步进、Python 绑定 |
| `BSDFOptimizer.cs.slang` | Compute Shader | GPU 端梯度计算着色器 |
| `BSDFViewer.cs.slang` | Compute Shader | GPU 端可视化着色器（三视口 BSDF 切片渲染） |
| `BSDFOptimizerHelpers.slang` | Shader 辅助模块 | 视口坐标转换、BSDF 切片几何计算辅助函数 |
| `BSDFOptimizerParams.slang` | Shader 公共类型 | `BSDFOptimizerParams` 共享参数结构体 |
| `CMakeLists.txt` | 构建文件 | CMake 插件注册与着色器拷贝配置 |

## 依赖关系

### 框架依赖
- `Falcor.h` -- Falcor 核心框架
- `RenderGraph/RenderPass.h` -- 渲染通道基类
- `RenderGraph/RenderPassStandardFlags.h` -- 标准刷新标志

### 可微渲染依赖
- `DiffRendering/SceneGradients.h` -- 场景梯度管理（材质梯度缓冲区的创建、清空、聚合）

### 功能模块依赖
- `Utils/Sampling/SampleGenerator.h` -- 伪随机数采样器
- `Rendering/Materials/BSDFConfig.slangh` -- BSDF 配置
- `fstd/span.h` -- span 容器（用于 Adam 优化器的参数传递）
- `Scene/Shading` -- 着色数据和顶点数据结构

### 输出通道
| 通道名 | 格式 | 说明 |
|--------|------|------|
| `output` | RGBA32Float | 三视口可视化输出（初始材质 / 差异 / 参考材质） |

## 关键类与接口

### `BSDFOptimizer` 类

继承自 `RenderPass`，通过 `FALCOR_PLUGIN_CLASS` 宏注册为 `"BSDFOptimizer"` 插件。

**核心方法：**
- `execute(RenderContext*, const RenderData&)` -- 每帧执行：若在优化则计算梯度并更新参数，然后渲染可视化
- `setScene(RenderContext*, const ref<Scene>&)` -- 场景加载，创建优化器和查看器 compute pass，初始化 `SceneGradients`
- `initOptimization()` -- 重置材质参数和 Adam 优化器状态
- `executeOptimizerPass(RenderContext*)` -- 执行 GPU 梯度计算 pass
- `step(RenderContext*)` -- 读回梯度并执行 Adam 优化步进
- `executeViewerPass(RenderContext*, const RenderData&)` -- 执行可视化渲染 pass
- `renderUI(Gui::Widgets&)` -- 渲染 UI（开始/停止/重置优化，材质选择）

**Python 绑定属性/方法：**
- `init_material_id` (只读) -- 获取初始材质 ID
- `ref_material_id` (只读) -- 获取参考材质 ID
- `bsdf_slice_resolution` (读写) -- 获取/设置 BSDF 切片分辨率
- `compute_bsdf_grads()` -- 手动执行一次梯度计算并返回梯度缓冲区

### `AdamOptimizer` 内部结构体

实现 Adam 自适应学习率优化器：
- `lr` (vector<float>) -- 每个参数的学习率
- `beta1` / `beta2` -- 矩估计指数衰减率（默认 0.9 / 0.999）
- `epsilon` -- 数值稳定项（默认 1e-6）
- `step(dx, x)` -- 根据梯度 `dx` 更新参数 `x`

### `BSDFOptimizerParams` 结构体

CPU/GPU 共享参数：

| 参数 | 类型 | 说明 |
|------|------|------|
| `frameDim` | uint2 | 帧缓冲区分辨率 |
| `frameCount` | uint | 帧计数 |
| `initViewPortOffset` | float2 | 初始材质视口偏移 |
| `diffViewPortOffset` | float2 | 差异视口偏移 |
| `refViewPortOffset` | float2 | 参考材质视口偏移 |
| `viewPortScale` | float2 | 视口缩放系数 |
| `bsdfTableDim` | uint2 | BSDF 切片表分辨率 |
| `initMaterialID` | uint | 初始（待优化）材质 ID |
| `refMaterialID` | uint | 参考（目标）材质 ID |

### 预设学习率

| 材质类型 | 参数 | 学习率 |
|----------|------|--------|
| PBRTDiffuse | diffuse | 1e-2 |
| PBRTConductor | eta, k, roughness | 1e-2 |
| Standard | base_color | 1e-2 |
| Standard | roughness, metallic | 3e-3 |
