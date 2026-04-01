# CPUSampleGenerator 源码文档

> 路径: `Source/Falcor/Utils/SampleGenerators/CPUSampleGenerator.h`
> 类型: C++ 头文件（纯虚基类，无 .cpp 实现）
> 模块: Utils/SampleGenerators

## 功能概述

定义了 **CPU 端二维采样模式生成器** 的抽象基类。用于在主机端生成二维采样点，常用于抗锯齿（AA）中的亚像素抖动、时间超采样等场景。各具体采样模式（如 DX MSAA 模式、Halton 序列、分层采样）均继承此基类。

## 类与接口

### `CPUSampleGenerator`

- **继承**: `Object`（Falcor 引用计数基类）
- **职责**: CPU 端二维采样模式生成器的抽象接口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual uint32_t getSampleCount() const = 0` | 返回采样模式中的总采样数 |
| `virtual void reset(uint32_t startID = 0) = 0` | 重置采样生成器，可选择从指定采样 ID 开始 |
| `virtual float2 next() = 0` | 返回下一个二维采样点，范围为每个维度 [-0.5, 0.5) |

#### 关键成员

无公有成员。构造函数为 `protected`，禁止直接实例化。

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义
- `Core/Object.h` — 引用计数基类
- `Utils/Math/Vector.h` — 向量类型（float2）

### 被以下文件引用

- `DxSamplePattern.h` — DirectX MSAA 采样模式
- `HaltonSamplePattern.h` — Halton 低差异序列采样
- `StratifiedSamplePattern.h` — 分层随机采样

## 实现细节

- 纯虚基类，不包含任何实现代码
- 采样值范围为 [-0.5, 0.5)，其中 0 对应像素中心，便于直接作为亚像素偏移使用
- 虚析构函数确保正确的多态销毁
