# AccumulatePass 源码文档

> 路径: `Source/RenderPasses/AccumulatePass/AccumulatePass.h` + `AccumulatePass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/AccumulatePass

## 功能概述

本文件实现了时序累积（Temporal Accumulation）渲染通道。该通道对输入纹理进行逐帧累积求平均，常用于蒙特卡洛路径追踪等需要多帧收敛的地真值（ground truth）渲染场景。

由于 fp32 精度在累积大量样本时可能不足，该通道支持三种精度模式：单精度标准求和、单精度补偿求和（Kahan 求和）和双精度标准求和。同时支持帧数上限和溢出处理策略。

## 类与接口

### `AccumulatePass`

- **继承**: `RenderPass`
- **职责**: 对输入数据进行时序累积求平均

#### 枚举类型

| 枚举 | 值 | 说明 |
|------|------|------|
| `Precision::Double` | 0 | 双精度标准求和 |
| `Precision::Single` | 1 | 单精度标准求和 |
| `Precision::SingleCompensated` | 2 | 单精度补偿求和（Kahan 求和） |
| `OverflowMode::Stop` | 0 | 达到帧数上限后停止累积，保留已累积图像 |
| `OverflowMode::Reset` | 1 | 达到上限后重置累积 |
| `OverflowMode::EMA` | 2 | 达到上限后切换为指数移动平均 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AccumulatePass(ref<Device>, const Properties&)` | 构造函数，解析属性并创建计算状态 |
| `Properties getProperties() const` | 序列化所有属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（input）和输出（output，RGBA32Float 格式） |
| `void execute(RenderContext*, const RenderData&)` | 执行累积：检查自动重置条件 -> 处理溢出 -> 执行累积 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI：启用开关、重置按钮、自动重置、精度模式、最大帧数、溢出模式 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景并重置累积 |
| `void onHotReload(HotReloadFlags)` | 着色器热重载时重置累积 |
| `void setEnabled(bool)` | 启用/禁用累积 |
| `void reset()` | 重置累积（将帧计数归零） |
| `void prepareAccumulation(RenderContext*, uint32_t, uint32_t)` | 准备/分配/清除中间缓冲区 |
| `void accumulate(RenderContext*, const ref<Texture>&, const ref<Texture>&)` | 执行 GPU 累积计算 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEnabled` | `bool` | 累积启用开关 |
| `mAutoReset` | `bool` | 场景变化和刷新标志时自动重置 |
| `mPrecisionMode` | `Precision` | 当前精度模式 |
| `mMaxFrameCount` | `uint32_t` | 最大累积帧数（0 = 无限） |
| `mOverflowMode` | `OverflowMode` | 溢出处理策略 |
| `mFrameCount` | `uint32_t` | 已累积帧数 |
| `mFrameDim` | `uint2` | 当前帧尺寸 |
| `mpLastFrameSum` | `ref<Texture>` | 上一帧累积和（Single/SingleCompensated 模式） |
| `mpLastFrameCorr` | `ref<Texture>` | 上一帧补偿项（SingleCompensated 模式） |
| `mpLastFrameSumLo` | `ref<Texture>` | 上一帧累积和低位（Double 模式） |
| `mpLastFrameSumHi` | `ref<Texture>` | 上一帧累积和高位（Double 模式） |
| `mpProgram` | `map<Precision, ref<Program>>` | 每种精度模式的计算程序 |
| `mOutputFormat` | `ResourceFormat` | 输出格式 |
| `mOutputSizeSelection` | `IOSize` | 输出尺寸选择 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心框架
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` — IO 尺寸辅助工具
- `RenderGraph/RenderPassStandardFlags.h` — 标准刷新标志
- 着色器: `Accumulate.cs.slang`

### 被以下文件引用

- 渲染图配置脚本通过 `AccumulatePass` 名称引用

## 实现细节

1. **自动重置条件**: 响应 `RenderPassRefreshFlags`（其他通道发出的刷新信号）和场景更新（排除相机抖动和历史更改）。相机属性变化中仅 Jitter 和 History 不触发重置。

2. **输入格式支持**: 支持 Float、Uint 和 Sint 三种输入格式类型，通过编译宏 `_INPUT_FORMAT` 切换。

3. **精度模式编译**: 三种精度模式使用不同的着色器入口点。补偿求和模式使用 `FloatingPointModePrecise` 编译标志禁止浮点重排序。

4. **中间缓冲区管理**: `prepareAccumulation` 根据当前精度模式分配所需的中间纹理，释放不需要的纹理。分辨率变化或重置时清除缓冲区。

5. **禁用时行为**: 当累积被禁用且输入为浮点格式时，直接 blit 输入到输出。当 I/O 尺寸不匹配时产生错误并禁用通道。

6. **兼容性**: 支持已废弃的 `enableAccumulation` 属性名称，自动映射到新的 `enabled` 属性。

7. **Python 脚本接口**: 通过 pybind11 暴露 `enabled` 属性和 `reset()` 方法。
