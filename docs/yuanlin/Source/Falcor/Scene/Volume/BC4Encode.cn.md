# BC4Encode 源码文档

> 路径: `Source/Falcor/Scene/Volume/BC4Encode.h`
> 类型: C++ 头文件
> 模块: Scene/Volume

## 功能概述

BC4 纹理压缩编码器，将 4x4 的 uint8 alpha 值块编码为单个 64 位 BC4 压缩块。该实现源自 libsquish 库的 alpha 压缩代码，用于体素网格的砖块纹理图集生成。

## 类与接口

本文件仅包含静态函数，无类定义。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `static void CompressAlphaDxt5(uint8_t* tile, void* block)` | 将 16 个 uint8 值（4x4 块）压缩为 BC4 格式的 64 位块 |
| `static void FixRange(int& min, int& max, int steps)` | 确保 min/max 范围至少有 steps 级 |
| `static int FitCodes(uint8_t* tile, uint8_t* codes, uint8_t* indices)` | 将每个 alpha 值拟合到码本，返回总误差 |
| `static void WriteAlphaBlock(int alpha0, int alpha1, uint8_t* indices, void* block)` | 写入 alpha 块（端点 + 3 位索引） |
| `static void WriteAlphaBlock5(int alpha0, int alpha1, uint8_t* indices, void* block)` | 写入 5-alpha 模式块（带端点交换） |
| `static void WriteAlphaBlock7(int alpha0, int alpha1, uint8_t* indices, void* block)` | 写入 7-alpha 模式块（带端点交换） |

## 依赖关系

### 本文件引用

- `<algorithm>`, `<cstdint>`, `<climits>` — 标准库

### 被以下文件引用

- `GridConverter.h` — NanoVDB 到砖块纹理转换器

## 实现细节

- 尝试两种编码方案（5-alpha 和 7-alpha 插值），选择误差最小的方案。
- 5-alpha 模式保留 0 和 255 作为特殊端点值。
- 7-alpha 模式在两个端点之间进行 7 级线性插值。
- 每个 4x4 块使用 8 字节：2 字节端点 + 6 字节（16 个 3 位索引）。
