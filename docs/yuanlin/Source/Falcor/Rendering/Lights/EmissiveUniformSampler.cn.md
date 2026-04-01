# EmissiveUniformSampler 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveUniformSampler.h` + `EmissiveUniformSampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件实现了均匀采样的自发光光源采样器。该采样器以相等的概率从所有活跃的发光三角形中随机选择一个进行采样，是最简单的采样策略。由于不考虑三角形的辐射功率或空间位置，对于功率分布不均匀的场景效率较低，但实现简单且无额外开销。

## 类与接口

### `EmissiveUniformSampler`

- **继承**: `EmissiveLightSampler`
- **职责**: 以均匀概率采样自发光三角形

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `EmissiveUniformSampler(RenderContext*, ref<ILightCollection>, const Options&)` | 构造函数，创建均匀采样器实例 |
| `const Options& getOptions() const` | 返回当前配置选项 |

### `EmissiveUniformSampler::Options`

- **职责**: 存储采样器配置选项（当前为空，预留扩展）

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mOptions` | `Options` | 配置选项 |

## 实现细节

- 构造函数调用基类构造函数，传入 `EmissiveLightSamplerType::Uniform` 类型标识
- 当前 Options 结构体为空，预留了 `serialize` 方法用于未来扩展序列化支持
- 由于均匀采样无需预处理数据，未重写 `update()` 和 `bindShaderData()` 方法

## 依赖关系

### 本文件引用

- `EmissiveLightSampler.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Utils/Properties.h` — 属性工具
- `Scene/Lights/LightCollection.h` — 光源集合

### 被以下文件引用

- `EmissiveLightSampler.slang` — 着色器端通过条件编译引用对应的 GPU 采样器
