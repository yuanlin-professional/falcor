# Light 源码文档

> 路径: `Source/Falcor/Scene/Lights/Light.h`, `Source/Falcor/Scene/Lights/Light.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Lights

## 功能概述

本文件定义了 Falcor 中所有解析光源（Analytic Light）的类层次结构。包括点光源、聚光灯、方向光、远距离光和多种面积光（矩形、圆盘、球形）。每种光源类型都继承自基类 `Light`，支持动画、UI 渲染、着色器数据绑定等功能。

## 类与接口

### `Light`

- **继承**: `Animatable`
- **职责**: 所有光源的抽象基类，定义通用的光源属性和接口（激活状态、强度、颜色、变化追踪等）。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual void bindShaderData(const ShaderVar& var)` | 将光源参数绑定到着色器变量 |
| `virtual void renderUI(Gui::Widgets& widget)` | 渲染光源的 UI 控件 |
| `virtual float getPower() const = 0` | 获取光源总功率（纯虚函数） |
| `void setActive(bool active)` | 激活/停用光源 |
| `void setIntensity(const float3& intensity)` | 设置光源强度（RGB） |
| `Changes beginFrame()` | 开始新帧，返回上一帧以来的变化标志 |
| `LightType getType() const` | 获取光源类型 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData` | `LightData` | 光源数据（与 GPU 共享的结构体） |
| `mPrevData` | `LightData` | 上一帧的光源数据，用于变化检测 |
| `mActive` | `bool` | 光源是否激活 |
| `mChanges` | `Changes` | 当前帧的变化标志位 |
| `mName` | `std::string` | 光源名称 |

### `PointLight`

- **继承**: `Light`
- **职责**: 点光源/聚光灯。支持世界空间位置、方向、锥形开口角度和半影角度。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setWorldPosition(const float3& pos)` | 设置世界空间位置 |
| `void setWorldDirection(const float3& dir)` | 设置世界空间方向 |
| `void setOpeningAngle(float openingAngle)` | 设置聚光灯的锥形半角（弧度） |
| `void setPenumbraAngle(float angle)` | 设置半影区域的半角（弧度） |
| `float getPower() const override` | 返回光源总功率：luminance * 4 * PI |

### `DirectionalLight`

- **继承**: `Light`
- **职责**: 方向光源，来自无限远处的平行光。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setWorldDirection(const float3& dir)` | 设置世界空间方向 |
| `float getPower() const override` | 返回 0（方向光无有限功率） |

### `DistantLight`

- **继承**: `Light`
- **职责**: 远距离光源，与方向光类似但占据非零立体角（如太阳）。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setAngle(float theta)` | 设置光源张角的半角 |
| `void setWorldDirection(const float3& dir)` | 设置世界空间方向 |

### `AnalyticAreaLight`

- **继承**: `Light`
- **职责**: 解析面积光的基类，支持缩放和变换矩阵。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setScaling(float3 scale)` | 设置光源缩放 |
| `void setTransformMatrix(const float4x4& mtx)` | 设置变换矩阵 |
| `float getPower() const override` | 返回 luminance * PI * surfaceArea |

### `RectLight`

- **继承**: `AnalyticAreaLight`
- **职责**: 矩形面积光源。

### `DiscLight`

- **继承**: `AnalyticAreaLight`
- **职责**: 圆盘面积光源。

### `SphereLight`

- **继承**: `AnalyticAreaLight`
- **职责**: 球形面积光源。

## 依赖关系

### 本文件引用

- `LightData.slang` — 光源数据结构定义
- `Core/Macros.h`, `Core/Program/ShaderVar.h` — 着色器变量绑定
- `Utils/UI/Gui.h` — UI 渲染
- `Utils/Color/ColorHelpers.slang` — 亮度计算
- `Scene/Animation/Animatable.h` — 动画支持
- `Utils/Scripting/ScriptBindings.h` — Python 脚本绑定

### 被以下文件引用

- `Scene/Scene.h` — 场景管理
- 各种渲染通道中的光源采样代码

## 实现细节

- `LightData` 结构体大小必须是 16 字节的倍数（用于 GPU 对齐）。
- `beginFrame()` 通过比较当前和上一帧的数据来检测位置、方向、强度、表面积等变化。
- UI 中的颜色和强度分离显示：内部使用 `mUiLightIntensityColor` 和 `mUiLightIntensityScale` 分别跟踪颜色和标量强度。
- 面积光的 `update()` 方法在缩放或变换变化后重新计算表面积。
- 所有光源类型都通过 `FALCOR_SCRIPT_BINDING` 宏导出到 Python 脚本系统。
