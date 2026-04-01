# ImGuiConfig 源码文档

> 路径: `Source/Falcor/Utils/UI/ImGuiConfig.h`
> 类型: C++ 头文件
> 模块: Utils/UI

## 功能概述

ImGuiConfig.h 是 ImGui 的配置文件，用于在 ImGui 的向量类型（`ImVec2`、`ImVec3`、`ImVec4`）和 Falcor 的数学向量类型（`float2`、`float3`、`float4`）之间提供隐式类型转换。通过 ImGui 提供的宏机制，在编译时将转换运算符注入到 ImGui 的向量类中。

## 类与接口

本文件不定义类，而是通过宏注入为 ImGui 向量类型添加扩展。

### 宏定义

| 宏 | 说明 |
|----|------|
| `IM_VEC2_CLASS_EXTRA` | 为 `ImVec2` 添加从 `Falcor::float2` 构造和向 `Falcor::float2` 转换的运算符 |
| `IM_VEC3_CLASS_EXTRA` | 为 `ImVec3` 添加从 `Falcor::float3` 构造和向 `Falcor::float3` 转换的运算符 |
| `IM_VEC4_CLASS_EXTRA` | 为 `ImVec4` 添加从 `Falcor::float4` 构造和向 `Falcor::float4` 转换的运算符 |

每个宏注入两个成员：
1. `constexpr` 构造函数：从 Falcor 向量构造 ImGui 向量
2. 类型转换运算符：将 ImGui 向量隐式转换为 Falcor 向量

## 依赖关系

### 本文件引用

- `Utils/Math/VectorTypes.h` — Falcor 的 `float2`、`float3`、`float4` 类型定义

### 被以下文件引用

- ImGui 编译时通过配置包含此文件（作为 `imconfig.h` 的替代或补充）
- 间接被所有使用 ImGui 的 Falcor 源文件影响

## 实现细节

- 此文件必须在 ImGui 头文件被包含之前生效，ImGui 通过 `IMGUI_USER_CONFIG` 或在其构建系统中引用此配置
- 使用 `constexpr` 构造函数确保编译期可用
- 隐式转换使得 Falcor 代码可以直接将 `float2` 等类型传递给 ImGui 函数，无需手动转换
