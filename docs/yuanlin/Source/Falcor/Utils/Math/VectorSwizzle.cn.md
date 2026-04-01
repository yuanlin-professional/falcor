# VectorSwizzle 源码文档

> 路径: `Source/Falcor/Utils/Math/VectorSwizzle2.inl.h` / `VectorSwizzle3.inl.h` / `VectorSwizzle4.inl.h`
> 类型: C++ 内联头文件
> 模块: Utils/Math

## 功能概述

为 2/3/4 维向量类型提供 HLSL/Slang 风格的 swizzle 访问方法。例如 `v.xy()`、`v.zyx()`、`v.xxxx()` 等。这些文件被 `VectorTypes.h` 中的向量模板特化所包含。

## 实现细节

- 每个文件为对应维度的向量生成所有合法的 swizzle 组合方法。
- 返回对应维度的新向量（只读访问）。
- 通过 `#include` 机制嵌入到向量类型的 class body 中。
