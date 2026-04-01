# StratifiedSamplePattern 源码文档

> 路径: `Source/Falcor/Utils/SampleGenerators/StratifiedSamplePattern.h` + `Source/Falcor/Utils/SampleGenerators/StratifiedSamplePattern.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/SampleGenerators

## 功能概述

实现了 **分层随机采样模式** 生成器。将采样空间划分为 M x N 的网格（bins），在每个网格内生成随机抖动的采样点。每轮采样开始前会随机置换网格的遍历顺序，以避免与低差异采样生成器产生相关性伪影。

采样器在每个 `getSampleCount()` 采样周期后会无限继续生成随机样本，因此在每个周期倍数后分布是均匀随机的。

## 类与接口

### `StratifiedSamplePattern`

- **继承**: `CPUSampleGenerator`
- **职责**: 将二维采样空间分层并在各层内随机采样

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<StratifiedSamplePattern> create(uint32_t sampleCount = 1)` | 静态工厂方法，创建分层采样生成器 |
| `StratifiedSamplePattern(uint32_t sampleCount)` | 构造函数。将 sampleCount 分解为 M x N 网格，M 和 N 尽量接近 |
| `uint32_t getSampleCount() const override` | 返回 `mBinsX * mBinsY`（总网格数） |
| `void reset(uint32_t startID = 0) override` | 重置采样状态。不支持从任意 ID 重启，始终从 0 开始 |
| `float2 next() override` | 返回下一个分层随机采样点，范围 [-0.5, 0.5) |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mBinsX` | `uint32_t` | X 方向的网格数 |
| `mBinsY` | `uint32_t` | Y 方向的网格数 |
| `mCurSample` | `uint32_t` | 当前采样索引 |
| `mRng` | `std::mt19937` | Mersenne Twister 随机数生成器，用于抖动和置换 |
| `mPermutation` | `std::vector<uint32_t>` | 网格遍历顺序的随机置换表 |

## 依赖关系

### 本文件引用

- `CPUSampleGenerator.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Core/Error.h` — 错误处理
- `Utils/Math/Vector.h` — 向量类型
- `Utils/Logger.h` — 日志输出
- `<random>`, `<vector>`, `<algorithm>` — 标准库

### 被以下文件引用

- 需要分层采样的渲染通道（如 TAA、路径追踪的像素采样等）

## 实现细节

- **网格分解**：将 sampleCount 分解为 M x N，其中 M 从 sqrt(sampleCount) 开始递增搜索，直到找到整除的因子。最坏情况下（sampleCount 为质数），结果为 sampleCount x 1
- **采样数量限制**：sampleCount 被限制在 [1, 1024] 范围内，防止置换表过大
- **随机置换**：每轮采样开始时（`mCurSample == 0`），使用 `std::shuffle` 随机置换网格遍历顺序
- **采样点生成**：在选定的网格 (i, j) 内，采样点为 `((i + u()) / mBinsX, (j + u()) / mBinsY) - 0.5`，其中 u() 为 [0, 1) 均匀随机数
- `reset()` 不支持从任意 startID 重启（会输出警告），始终从 0 开始并重置随机数生成器
