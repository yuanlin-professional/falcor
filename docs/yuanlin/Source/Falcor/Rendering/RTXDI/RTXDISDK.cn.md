# RTXDISDK 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/RTXDISDK.cpp`
> 类型: C++ 实现文件
> 模块: Rendering/RTXDI

## 功能概述

本文件是 RTXDI SDK 源码的编译桥接文件。它通过 `#include` 直接包含 RTXDI SDK 的核心实现文件 `RTXDI.cpp`（位于 `external/packman/rtxdi/rtxdi-sdk/src/`），使 SDK 的 C++ 代码能够在 Falcor 的编译系统中被编译链接。

整个文件使用 `FALCOR_HAS_RTXDI` 宏进行条件编译。如果 RTXDI SDK 未安装，此文件编译为空。

## 依赖关系

### 本文件引用

- `external/packman/rtxdi/rtxdi-sdk/src/RTXDI.cpp`（RTXDI SDK 核心实现）

### 被以下文件引用

- 仅作为编译单元参与链接，无头文件被其他代码引用

## 实现细节

- 采用 `#include` 直接包含外部 SDK 源码的方式（unity build 模式），避免在 Falcor 构建系统中单独添加 SDK 的编译目标
- 如果 include 路径无法解析，说明 RTXDI SDK 未正确安装
