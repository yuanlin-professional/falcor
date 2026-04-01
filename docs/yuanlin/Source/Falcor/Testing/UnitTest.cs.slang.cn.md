# UnitTest.cs.slang 着色器文档

> 路径: `Source/Falcor/Testing/UnitTest.cs.slang`
> 类型: Slang 计算着色器
> 模块: Testing (测试框架)

## 功能概述

单元测试框架的示例计算着色器，用于 `GPU_TEST(TestGPUTest)` 测试用例。该着色器接收一个整数值和缩放因子，将缩放后的索引值写入结构化缓冲区，用于验证 GPU 测试框架的基本功能。

## 结构体与接口

### 常量缓冲区 `TestCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `nValues` | `int` | 要处理的值数量 |
| `scale` | `float` | 缩放因子 |

### 资源

| 名称 | 类型 | 说明 |
|------|------|------|
| `result` | `RWStructuredBuffer<float>` | 输出结构化缓冲区，存储计算结果 |

## 函数

### `void main()`

- **说明**: 计算着色器入口函数。遍历 `nValues` 个索引，将 `scale * i` 写入 `result[i]`。
- **参数**: 无（从 `TestCB` 常量缓冲区读取参数）
- **输出**: 写入 `result` 缓冲区

## 依赖关系

### import

无外部依赖。

## 实现细节

- **执行方式**: 作为单线程计算着色器运行（未声明 `[numthreads]`，使用默认值），通过循环处理所有值。
- **用途**: 配合 `GPUUnitTestContext` 验证结构化缓冲区的分配、着色器变量绑定和数据回读功能是否正常。
- **测试验证**: 对应的 CPU 端测试代码读回 `result` 缓冲区，验证 `result[i] == scale * i`（其中 `scale = 2.0`）。
