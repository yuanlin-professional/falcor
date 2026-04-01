# Composite 源码文档

> 路径: `Source/RenderPasses/Utils/Composite/Composite.h` + `Composite.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/Utils/Composite

## 功能概述

Composite 是一个简单的合成渲染通道，将两个输入缓冲区 A 和 B 按可配置的方式混合输出。支持加法（Add）和乘法（Multiply）两种混合模式，每个输入可独立缩放。输出格式支持浮点、无符号整数和有符号整数，当输出为整数格式时使用四舍五入到最近偶数进行转换。

计算公式：
- **加法模式**: `C = scaleA * A + scaleB * B`
- **乘法模式**: `C = (scaleA * A) * (scaleB * B)`

## 类与接口

### `Composite`

- **继承**: `RenderPass`
- **职责**: 两缓冲区混合合成

#### 枚举 `Composite::Mode`

| 值 | 说明 |
|----|------|
| `Add` | 加法混合 |
| `Multiply` | 乘法混合 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Composite(ref<Device>, const Properties&)` | 构造函数，解析属性并创建计算通道 |
| `Properties getProperties() const` | 序列化模式、缩放系数和输出格式 |
| `RenderPassReflection reflect(const CompileData&)` | 声明可选输入 A、B 和输出 out |
| `void compile(RenderContext*, const CompileData&)` | 获取默认纹理尺寸 |
| `void execute(RenderContext*, const RenderData&)` | 设置宏定义、绑定资源并执行计算着色器 |
| `void renderUI(Gui::Widgets&)` | 模式下拉框和缩放系数控件 |
| `DefineList getDefines() const` | 根据当前模式和输出格式生成着色器宏定义 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mMode` | `Mode` | 混合模式 |
| `mScaleA` / `mScaleB` | `float` | 输入 A/B 的缩放系数 |
| `mOutputFormat` | `ResourceFormat` | 输出纹理格式 |
| `mFrameDim` | `uint2` | 帧尺寸 |
| `mCompositePass` | `ref<ComputePass>` | 计算通道 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `Core/Enum.h`
- `RenderGraph/RenderPass.h`
- `CompositeMode.slangh`（共享宏定义）
- `Composite.cs.slang`（着色器）

### 被以下文件引用

- `Utils.cpp`（插件注册）
