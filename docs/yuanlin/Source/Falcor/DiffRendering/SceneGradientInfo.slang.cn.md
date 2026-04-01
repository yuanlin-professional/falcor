# SceneGradientInfo.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/SceneGradientInfo.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件定义了可微渲染中场景梯度信息的核心数据结构，包括梯度模式枚举 `GradientMode`、可微标志 `SceneGradientFlag` 和场景梯度信息 `SceneGradientInfo`。`SceneGradientFlag` 通过高阶微分机制控制梯度的传播方向和模式，是实现选择性梯度传播（如关闭梯度、前向调试等）的关键。

## 结构体与接口

### `GradientMode` (枚举)

| 值 | 说明 |
|-----|------|
| `None` | 不传播梯度 |
| `Scene` | 正常传播梯度到场景参数 |
| `ForwardDebug` | 使用前向模式梯度进行调试 |

### `SceneGradientFlag`

- **实现接口**: `IDifferentiable`
- **职责**: 可微标志，通过高阶微分控制梯度传播模式。主要用于：(1) 将标志设为 `None` 以零化 Jacobian 计算中的梯度；(2) 设为 `ForwardDebug` 注入前向调试梯度

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `gradMode` | `GradientMode` | 当前梯度模式 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static SceneGradientFlag make(GradientMode mode)` | 创建标志（带自定义前向/反向导数） |
| `static SceneGradientFlag dadd(a, b)` | 可微加法（默认返回 Scene 模式） |
| `static SceneGradientFlag dmul(a, b)` | 可微乘法（默认返回 Scene 模式） |
| `static SceneGradientFlag dzero()` | 可微零值（默认返回 Scene 模式） |

### `SceneGradientInfo`

- **实现接口**: `IDifferentiable`
- **职责**: 封装梯度传播所需的上下文信息

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `flag` | `SceneGradientFlag` | 可微梯度标志 |
| `pixel` | `uint2` | 当前像素坐标 |
| `pixelID` | `uint` | 像素的线性索引 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Scene.SceneTypes` | 场景类型定义 |

### 被以下文件引用

- `DiffRendering/DiffSceneIO.slang`（导出引用）
- `DiffRendering/DiffSceneQuery.slang`
