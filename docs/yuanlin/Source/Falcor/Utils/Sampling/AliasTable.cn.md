# AliasTable 源码文档

> 路径: `Source/Falcor/Utils/Sampling/AliasTable.h` + `Source/Falcor/Utils/Sampling/AliasTable.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Sampling

## 功能概述

实现了 **别名方法（Alias Method）**，用于从离散概率分布中进行采样。别名表允许在 O(1) 时间内根据给定的权重分布进行随机采样，构建过程采用 Vose 1991 年提出的 O(N) 线性算法。

核心思想是将每个别名表条目由一个过重样本和一个不足样本组合而成。首先将所有输入权重分为两组（高于平均值和低于平均值），然后依次合并两组中的首元素，残余样本根据其权重重新归类。

## 类与接口

### `AliasTable`

- **继承**: 无
- **职责**: 在 CPU 端构建别名表并上传到 GPU 缓冲区，供着色器中高效离散概率采样使用

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `AliasTable(ref<Device> pDevice, std::vector<float> weights, std::mt19937& rng)` | 构造函数。接收 GPU 设备、权重向量和随机数生成器，使用 Vose O(N) 算法构建别名表并创建 GPU 缓冲区。权重无需归一化。 |
| `void bindShaderData(const ShaderVar& var) const` | 将别名表数据（items、weights、count、weightSum）绑定到着色器变量 |
| `uint32_t getCount() const` | 返回表中权重条目的数量 |
| `double getWeightSum() const` | 返回所有权重的总和 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCount` | `uint32_t` | 别名表中条目数量 |
| `mWeightSum` | `double` | 所有权重的总和（使用 double 以减少精度问题） |
| `mpItems` | `ref<Buffer>` | GPU 结构化缓冲区，存储别名表条目（Item 结构体） |
| `mpWeights` | `ref<Buffer>` | GPU 结构化缓冲区，存储原始权重 |

### `AliasTable::Item`（内部结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `threshold` | `float` | 阈值。若 rand() < threshold，选择 indexB，否则选择 indexA |
| `indexA` | `uint32_t` | "重定向"索引，当均匀采样会过度加权 indexB 时使用 |
| `indexB` | `uint32_t` | 原始/置换索引，在 [0, mCount-1] 范围内均匀采样 |
| `_pad` | `uint32_t` | 内存对齐填充 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义（FALCOR_API 等）
- `Core/API/Buffer.h` — GPU 缓冲区抽象
- `Core/Program/ShaderVar.h` — 着色器变量绑定
- `Core/Error.h` — 错误处理（FALCOR_THROW）
- `Core/API/Device.h` — GPU 设备抽象
- `<memory>`, `<random>` — 标准库

### 被以下文件引用

- `AliasTable.slang` — GPU 端别名表采样实现
- 各种需要离散概率采样的渲染模块（如光源采样）

## 实现细节

- 构建算法基于 Vose 1991 论文："A linear algorithm for generating random numbers with a given distribution"
- 权重总和使用 `double` 精度累加，以减少浮点精度问题
- 元素数量不能超过 `uint32_t` 最大值（`0xFFFFFFFFu` 被保留为构建过程中的无效标记）
- 处理了边界情况：当剩余元素权重接近或等于平均权重时，将其视为精确等于平均权重来处理
- 代码注释中提到了可能的优化方向：通过隐式存储 indexB 将 Item 结构体从 128 位压缩到 64 位
