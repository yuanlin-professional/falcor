# EmissiveUniformSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveUniformSampler.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件实现了均匀采样的自发光光源采样器的 GPU 端逻辑。从所有活跃的发光三角形中以相等概率随机选择一个，然后在该三角形上进行均匀面积采样。这是最简单的采样策略，适用于光源分布较均匀的场景。

## 结构体与接口

### `EmissiveUniformSampler`

- **实现**: `IEmissiveLightSampler`
- **职责**: GPU 端以均匀概率采样发光三角形

| 成员 | 类型 | 说明 |
|------|------|------|
| `_dummy` | `uint4` | 占位数据，确保结构体作为其他结构体成员时正确运作 |

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `bool sampleLight<S>(...)` | 均匀随机选择一个活跃三角形，然后在其上均匀面积采样 |
| `float evalTriangleSelectionPdf(...)` | 返回 `1 / activeTriangleCount`，即均匀分布的选择概率 |
| `float evalPdf(...)` | 返回三角形选择概率与面积采样 PDF 的乘积 |

## 实现细节

### `sampleLight`

1. 检查活跃三角形数量是否为零
2. 用随机数均匀选择索引 `idx`，通过 `activeTriangles[idx]` 获取实际三角形索引
3. 三角形选择 PDF = `1 / triangleCount`
4. 在选中三角形上均匀面积采样
5. 最终 PDF = 选择 PDF * 面积采样 PDF

### 注意事项

- 使用 `getActiveTriangleCount()` 获取活跃三角形数量（排除了不可见/零通量的三角形）
- 随机数乘以数量后使用 `min` 防止浮点舍入导致越界

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Scene` — 场景全局变量
- `Utils.Sampling.SampleGeneratorInterface` — 样本生成器接口
- `Rendering.Lights.EmissiveLightSamplerHelpers` — 三角形采样辅助函数
- `Rendering.Lights.EmissiveLightSamplerInterface` — 采样器接口定义
