# CurveConfig.h 源码文档

> 路径: `Source/Falcor/Scene/Curves/CurveConfig.h`
> 类型: C++ 头文件
> 模块: Scene/Curves

## 功能概述

定义曲线细分（Tessellation）模式的枚举类型。该文件为曲线渲染提供两种细分策略的配置选项，是曲线模块的基础配置文件。

## 类与接口

### `CurveTessellationMode`（枚举类）

- **命名空间**: `Falcor`
- **职责**: 定义曲线几何体的细分模式，决定曲线如何被转换为可渲染的几何图元。

#### 枚举值

| 枚举值 | 说明 |
|--------|------|
| `LinearSweptSphere` | 线性扫掠球模式。将曲线表示为一系列线性连接的球体段（swept sphere），适用于光线追踪硬件加速的曲线相交测试。 |
| `PolyTube` | 多边形管道模式。将曲线细分为三角网格管道（poly-tube），通过在横截面上采样多个点生成三角面片，适用于传统光栅化渲染。 |

## 依赖关系

### 本文件引用

- 无外部依赖（仅使用标准 `#pragma once`）

### 被以下文件引用

- `CurveTessellation.h` / `CurveTessellation.cpp`：使用该枚举选择细分策略
- 场景加载模块：根据用户配置选择曲线细分模式

## 实现细节

- 该文件极其简洁，仅包含一个 `enum class` 定义
- 使用 `enum class` 而非普通 `enum`，提供类型安全的枚举
- 位于 `Falcor` 命名空间内，遵循项目统一命名规范
