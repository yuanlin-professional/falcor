# USDUtils 源码文档

> 路径: `Source/Modules/USDUtils/USDUtils.h`, `Source/Modules/USDUtils/USDUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: USDUtils

## 功能概述

USDUtils 模块的核心工具集合，提供 USD 与 Falcor 之间的类型转换函数、USD 属性读取辅助函数、着色器输入追踪、诊断消息委托、作用域守卫以及哈希组合工具。`.cpp` 文件仅包含头文件引用，所有实现均为内联函数。

## 类与接口

### `UsdObjHash`

- **职责**: 为 `UsdObject` 提供哈希函数，使其可用作 `unordered_map` 的键。

### `Float3Hash`

- **职责**: 为 `float3` 提供简单的哈希函数，使用素数乘法与异或组合。

### `DiagDelegate`

- **继承**: `TfDiagnosticMgr::Delegate`
- **职责**: 将 USD 的诊断消息（错误、警告、状态、致命错误）路由到 Falcor 的日志系统。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void IssueFatalError(...)` | 将致命错误转为 `FALCOR_THROW` |
| `void IssueError(...)` | 转发为 `logError` |
| `void IssueWarning(...)` | 转发为 `logWarning` |
| `void IssueStatus(...)` | 转发为 `logInfo` |

### `ScopeGuard`

- **职责**: RAII 作用域守卫，析构时执行指定的回调函数。

## 工具函数

| 函数签名 | 说明 |
|----------|------|
| `float3 toFalcor(const GfVec3f/GfVec3d/GfVec3h&)` | USD 三维向量转 Falcor `float3` |
| `float4x4 toFalcor(const GfMatrix4d/GfMatrix4f&)` | USD 矩阵转 Falcor `float4x4`（含转置，因 USD 使用行向量、Falcor 使用列向量） |
| `bool getLocalTransform(const UsdGeomXformable&, float4x4&)` | 获取可变换 prim 的局部变换矩阵 |
| `T getAttribute(const UsdAttribute&, const T& def)` | 读取 USD 属性值，未定义时返回默认值 |
| `T getAuthoredAttribute(...)` | 优先读取主属性，回退到备选属性 |
| `TfToken getPurpose(const UsdGeomImageable&)` | 获取 prim 的 purpose 属性 |
| `bool isRenderable(const UsdGeomImageable&)` | 判断 prim 是否可渲染（排除 guide/proxy，检查可见性） |
| `bool isTimeSampled(const UsdGeomPointBased&)` | 判断几何体是否含时间采样数据 |
| `UsdShadeInput getSourceInput(...)` | 追踪着色器输入的最终数据源 |
| `SdfLayerHandle getLayerHandle(...)` | 获取定义给定属性的 USD 层句柄 |
| `void hash_combine(size_t& seed, const T& v, Args... args)` | 可变参数哈希组合函数（基于 boost::hash_combine 算法） |

## 依赖关系

### 本文件引用

- `USDHelpers.h` - 警告宏
- `Core/Error.h` - 错误处理
- `Utils/Logger.h` - 日志系统
- `Utils/Math/Vector.h`、`Utils/Math/Matrix.h` - 数学类型
- USD 头文件: `usdGeom`、`usdShade` 等

### 被以下文件引用

- 几乎所有 USDUtils 模块文件和 USDImporter 都引用此头文件

## 实现细节

- 矩阵转换时进行转置：USD 使用行主序 + 行向量（v*M），Falcor 使用行主序 + 列向量（M*v）。
- `getSourceInput` 会递归追踪连接的着色器输入，优先返回类型为 `Asset` 的输入源。
- `isRenderable` 调用 `ComputeVisibility()` 来计算继承可见性（文档指出此操作可能低效，但实际中尚未发现性能问题）。
