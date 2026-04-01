# CrossFade 源码文档

> 路径: `Source/RenderPasses/Utils/CrossFade/CrossFade.h` + `CrossFade.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/Utils/CrossFade

## 功能概述

CrossFade 是一个简单的交叉淡入淡出渲染通道，用于在两个输入缓冲区之间进行时间相关的混合过渡。支持自动淡入模式（随帧数自动从 A 过渡到 B）和手动固定混合因子模式。当场景发生变化时自动重置过渡。

输出公式: `output = (1 - t) * A + t * B`，其中 `t` 为淡入因子。

## 类与接口

### `CrossFade`

- **继承**: `RenderPass`
- **职责**: 在两个输入之间进行时间相关的交叉淡入淡出

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `CrossFade(ref<Device>, const Properties&)` | 构造函数，解析属性并创建计算通道 |
| `Properties getProperties() const` | 序列化输出格式和淡入参数 |
| `RenderPassReflection reflect(const CompileData&)` | 声明可选输入 A、B 和输出 out |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景引用（用于变化检测） |
| `void compile(RenderContext*, const CompileData&)` | 获取默认纹理尺寸 |
| `void execute(RenderContext*, const RenderData&)` | 检测场景变化、计算混合因子、执行着色器 |
| `void renderUI(Gui::Widgets&)` | 自动淡入开关、等待帧数、淡入帧数、固定因子 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEnableAutoFade` | `bool` | 是否启用自动淡入（默认 true） |
| `mWaitFrameCount` | `uint32_t` | 自动淡入前等待的帧数（默认 10） |
| `mFadeFrameCount` | `uint32_t` | 自动淡入持续的帧数（默认 100） |
| `mFadeFactor` | `float` | 手动模式下的固定混合因子（默认 0.5） |
| `mMixFrame` | `uint32_t` | 当前混合帧计数 |
| `mpScene` | `ref<Scene>` | 场景引用 |
| `mOutputFormat` | `ResourceFormat` | 输出格式（不支持整数格式） |
| `mpFadePass` | `ref<ComputePass>` | 计算通道 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`
- `RenderGraph/RenderPassStandardFlags.h`
- `CrossFade.cs.slang`（着色器）

### 被以下文件引用

- `Utils.cpp`（插件注册）

## 实现细节

1. **场景变化检测**: 检查 `RenderPassRefreshFlags`、场景更新标志和相机变化（排除抖动和历史记录变化），触发重置。
2. **自动淡入公式**: `t = clamp((mMixFrame - mWaitFrameCount) / mFadeFrameCount, 0, 1)`
3. **整数格式限制**: 输出不支持整数格式，执行时会进行检查。
