# EmissivePowerSampler 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissivePowerSampler.h` + `EmissivePowerSampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件实现了基于发射功率的自发光光源采样器。该采样器按照每个发光三角形的辐射通量（flux）成比例地进行采样，通过 Alias Table（别名表）算法实现 O(1) 时间复杂度的加权随机采样。Alias Table 使用压缩的半精度浮点阈值和 24 位索引进行紧凑存储，最多支持 2^24（约 1600 万）个条目。

## 类与接口

### `EmissivePowerSampler`

- **继承**: `EmissiveLightSampler`
- **职责**: 按辐射功率成比例采样自发光三角形

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `EmissivePowerSampler(RenderContext* pRenderContext, ref<ILightCollection> pLightCollection)` | 构造函数，创建功率采样器实例 |
| `virtual bool update(RenderContext* pRenderContext, ref<ILightCollection> pLightCollection) override` | 更新采样器，当光源集合变化时重建 Alias Table |
| `virtual void bindShaderData(const ShaderVar& var) const override` | 将 `invWeightsSum` 和 `triangleAliasTable` 绑定到着色器 |
| `AliasTable generateAliasTable(std::vector<float> weights)` | 根据权重数组生成 Alias Table |

### `EmissivePowerSampler::AliasTable`

- **职责**: 存储压缩的 Alias Table 数据

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `weightSum` | `float` | 所有元素权重总和 |
| `N` | `uint32_t` | Alias Table 条目数量 |
| `fullTable` | `ref<Buffer>` | 压缩/打包的合并表缓冲区，每条目为 `uint2` |

#### 其他关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mNeedsRebuild` | `bool` | 标记是否需要在下次 update 时重建，初始为 true |
| `mAliasTableRng` | `std::mt19937` | 用于 Alias Table 随机化排列的随机数生成器 |
| `mTriangleTable` | `AliasTable` | 三角形采样的 Alias Table |

## 实现细节

- **update()**: 检测光源集合变化，获取所有发光三角形的 flux 作为权重，调用 `generateAliasTable` 重建表
- **generateAliasTable()**:
  1. 归一化权重使平均值为 1
  2. 按权重排序，使用双指针法构建经典 Alias Table（阈值 + 重定向）
  3. 随机化打乱条目顺序，减少系统性偏差
  4. 将数据压缩为 `uint2` 格式：16 位半精度阈值 + 2 个 24 位索引
  5. 上传到 GPU 缓冲区

## 依赖关系

### 本文件引用

- `EmissiveLightSampler.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Scene/Lights/LightCollection.h` — 光源集合
- `Utils/Timing/Profiler.h` — 性能分析（仅 .cpp）
- `<random>`, `<vector>`, `<algorithm>` — 标准库

### 被以下文件引用

- `EmissiveLightSampler.slang` — 着色器端通过条件编译引用对应的 GPU 采样器
