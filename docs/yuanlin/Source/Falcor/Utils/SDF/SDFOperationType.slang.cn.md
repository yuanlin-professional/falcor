# SDFOperationType.slang 着色器文档

> 路径: `Source/Falcor/Utils/SDF/SDFOperationType.slang`
> 类型: Slang 头文件（`.slangh` 风格，使用 `#pragma once`）
> 模块: Utils/SDF

## 功能概述

定义了有符号距离场（SDF）操作类型的枚举，列举了所有支持的 CSG（构造实体几何）布尔运算类型。该枚举为主机端和设备端共享类型，可同时在 C++ 和着色器代码中使用。

## 结构体与接口

### `SDFOperationType`（枚举类）

- **命名空间**: `Falcor`
- **职责**: 定义 SDF 布尔运算的类型标识，用于在运行时选择不同的 CSG 操作。

| 枚举值 | 说明 |
|--------|------|
| `Union` | 并集运算 — 合并两个 SDF 对象 |
| `Subtraction` | 差集运算 — 从一个 SDF 对象中减去另一个 |
| `Intersection` | 交集运算 — 取两个 SDF 对象的公共部分 |
| `SmoothUnion` | 平滑并集 — 带平滑过渡的并集 |
| `SmoothSubtraction` | 平滑差集 — 带平滑过渡的差集 |
| `SmoothIntersection` | 平滑交集 — 带平滑过渡的交集 |
| `Count` | 枚举计数，表示操作类型总数 |

## 依赖关系

### include

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏定义（`BEGIN_NAMESPACE_FALCOR`、`END_NAMESPACE_FALCOR`）
