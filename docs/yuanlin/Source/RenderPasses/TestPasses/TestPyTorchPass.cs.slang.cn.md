# TestPyTorchPass.cs.slang 源码文档

> 路径: `Source/RenderPasses/TestPasses/TestPyTorchPass.cs.slang`
> 类型: 计算着色器
> 模块: RenderPasses/TestPasses

## 功能概述

此着色器为 TestPyTorchPass 提供数据生成和验证的 GPU 计算核心。`writeBuffer` 入口生成确定性的测试数据，`readBuffer` 入口验证缓冲区内容是否符合预期值。

## 结构体与接口

### 常量缓冲区 `CB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `dim` | `uint3` | 三维数据维度 |
| `offset` | `uint` | 值偏移量 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `bufferSrv` | `StructuredBuffer<float>` | 只读结构化缓冲区（验证用） |
| `bufferUav` | `RWStructuredBuffer<float>` | 读写结构化缓冲区（生成用） |
| `counter` | `RWByteAddressBuffer` | 原子计数器缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint getIndex(uint3 threadID)` | 计算三维线程 ID 到一维缓冲区索引的映射：`(x * dim.y + y) * dim.z + z` |
| `void writeBuffer(uint3 threadID)` | 写入入口 [16,16,1]：`bufferUav[idx] = float(offset + idx)` |
| `void readBuffer(uint3 threadID)` | 读取入口 [1,16,16]：验证值是否等于 `float(offset + idx)`，匹配则原子计数器+1 |

## 依赖关系 / import

- 无外部依赖

## 实现细节

1. **确定性数据**: 生成的值为 `float(offset + linearIndex)`，方便端到端验证。
2. **不同线程组配置**: `writeBuffer` 使用 [16,16,1]，`readBuffer` 使用 [1,16,16]，测试不同的线程配置。
3. **原子计数**: 使用 `InterlockedAdd` 原子操作统计匹配元素数。
