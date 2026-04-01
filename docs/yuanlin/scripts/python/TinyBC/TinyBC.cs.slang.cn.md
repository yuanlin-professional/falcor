# TinyBC.cs.slang 着色器文档

> 路径: `scripts/python/TinyBC/TinyBC.cs.slang`
> 类型: Slang 计算着色器
> 模块: scripts/python/TinyBC

## 功能概述

该计算着色器实现了基于梯度下降的 Block Compression（BC）纹理压缩编码器。对于输入纹理的每个 4x4 纹素块，着色器通过可微分的 L2 损失函数和反向传播，迭代优化插值权重与端点颜色。支持可选的 Adam 优化器以加速收敛。在优化的后期阶段（85% 进度后），权重被量化并冻结，仅对端点进行微调。最终将量化后的解码结果写入输出纹理。线程组大小为 4x8x1，每个线程处理一个 4x4 的纹素块。

## 编译时常量

| 名称 | 来源 | 说明 |
|------|------|------|
| `kUseAdam` | `USE_ADAM`（宏定义） | 是否启用 Adam 优化器 |
| `kNumOptimizationSteps` | `NUM_OPTIMIZATION_STEPS`（宏定义） | 梯度下降迭代总步数 |

## 全局资源

| 名称 | 类型 | 说明 |
|------|------|------|
| `lr` | `uniform float` | 学习率 |
| `adamBeta1` | `uniform float` | Adam 优化器的一阶矩衰减系数 |
| `adamBeta2` | `uniform float` | Adam 优化器的二阶矩衰减系数 |
| `textureDim` | `uniform int2` | 输入纹理的尺寸（宽度, 高度） |
| `gInputTex` | `Texture2D<float4>` | 未压缩的输入纹理（只读） |
| `gDecodedTex` | `RWTexture2D<float4>` | 解码后的 BC 纹理输出（可读写） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void l2Loss(BCTile tile, no_diff float4 target[16], out float loss[16])` | 计算 BC 块解码结果与目标颜色之间的逐纹素 L2 损失（支持反向微分） |
| `void encoder(int3 dispatchThreadId: SV_DispatchThreadID)` | 计算着色器入口函数。执行完整的 BC 压缩流程：加载目标纹素、初始化端点为块内最小/最大值、迭代梯度下降优化、量化并输出解码结果 |

## 算法流程

1. **初始化**：加载 4x4 块的 16 个纹素，计算最小/最大端点，权重初始化为 0.5
2. **迭代优化**（共 `kNumOptimizationSteps` 步）：
   - 前向 pass：调用 `l2Loss` 计算每个纹素的 L2 损失
   - 反向 pass：通过 `__bwd_diff(l2Loss)` 自动计算梯度
   - 可选使用 Adam 优化器进行梯度修正（含偏差校正）
   - 梯度下降更新 `tile = tile - lr * grad`，并钳制到有效范围
   - 在 85% 进度时量化并冻结权重，后续仅微调端点
3. **输出**：最终量化所有参数，使用精确解码（`decodeExact`）写入输出纹理

## 依赖关系

### import

- `BCTypes` -- 提供 `BCTile` 和 `BCWeights` 结构体定义及相关运算符重载，是 BC 压缩的核心数据类型模块
