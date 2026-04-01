# EmissiveLightSampler 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveLightSampler.h` + `EmissiveLightSampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件定义了自发光光源采样器的基类 `EmissiveLightSampler`，为所有自发光三角形采样器提供统一的接口规范。所有派生采样器（Uniform、Power、LightBVH）均遵循此接口，使其可以互相替换。基类管理了对 `ILightCollection`（光源集合）的引用以及更新标志的信号连接机制。

## 类与接口

### `EmissiveLightSampler`

- **继承**: 无（基类）
- **职责**: 为自发光光源采样器提供统一接口，管理光源集合引用和更新状态

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual bool update(RenderContext* pRenderContext, ref<ILightCollection> pLightCollection)` | 将采样器更新到当前帧，默认返回 false |
| `virtual DefineList getDefines() const` | 返回使用此采样器所需的着色器宏定义列表，设置 `_EMISSIVE_LIGHT_SAMPLER_TYPE` |
| `virtual void bindShaderData(const ShaderVar& var) const` | 将采样器数据绑定到着色器变量 |
| `virtual bool renderUI(Gui::Widgets& widget)` | 渲染 GUI 界面，返回是否有影响渲染的设置变化 |
| `EmissiveLightSamplerType getType() const` | 返回采样器的具体类型枚举值 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mType` | `const EmissiveLightSamplerType` | 自发光采样器类型，参见 EmissiveLightSamplerType.slangh |
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mpLightCollection` | `ref<ILightCollection>` | 光源集合引用 |
| `mUpdateFlagsConnection` | `sigs::Connection` | 更新标志信号连接 |
| `mLightCollectionUpdateFlags` | `ILightCollection::UpdateFlags` | 光源集合更新标志 |

### 实现细节

- **构造函数**: 接受采样器类型和光源集合，通过 `setLightCollection` 建立信号连接
- **setLightCollection**: 当光源集合改变时，断开旧连接并建立新的更新标志信号连接，通过 lambda 累积更新标志
- **getDefines**: 返回包含 `_EMISSIVE_LIGHT_SAMPLER_TYPE` 宏定义的列表，值为采样器类型的数值表示

## 依赖关系

### 本文件引用

- `EmissiveLightSamplerType.slangh` — 采样器类型枚举定义
- `Core/Macros.h` — Falcor 宏定义
- `Core/Program/DefineList.h` — 着色器宏定义列表
- `Core/Program/Program.h` — 程序相关（仅 .cpp）
- `Scene/Lights/LightCollection.h` — 光源集合接口
- `Utils/Scripting/ScriptBindings.h` — 脚本绑定（仅 .cpp）

### 被以下文件引用

- `EmissivePowerSampler.h` — 功率采样器继承自本类
- `EmissiveUniformSampler.h` — 均匀采样器继承自本类
- `LightBVHSampler.h` — BVH 采样器继承自本类
