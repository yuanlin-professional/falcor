# Animatable 源码文档

> 路径: `Source/Falcor/Scene/Animation/Animatable.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Animation

## 功能概述

定义可动画对象的抽象基类。任何需要通过场景图节点驱动变换的对象（如相机、光源等）均继承此类。提供动画启用/禁用控制、场景图节点关联和变换更新的虚接口。

## 类与接口

### `Animatable`

- **继承**: `Object`
- **职责**: 为场景中可接受动画驱动的对象提供统一接口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setHasAnimation(bool)` | 标记对象是否拥有动画数据 |
| `bool hasAnimation() const` | 查询是否有动画数据 |
| `void setIsAnimated(bool)` | 启用/禁用动画 |
| `bool isAnimated() const` | 查询动画是否启用 |
| `void setNodeID(NodeID)` | 设置关联的场景图节点 ID |
| `NodeID getNodeID() const` | 获取场景图节点 ID |
| `virtual void updateFromAnimation(const float4x4&) = 0` | 纯虚函数：从动画变换矩阵更新对象状态 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mHasAnimation` | `bool` | 是否拥有动画数据（默认 false） |
| `mIsAnimated` | `bool` | 动画是否启用（默认 true） |
| `mNodeID` | `NodeID` | 关联的场景图节点（默认无效） |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏
- `Core/Object.h` — 基础对象类
- `Scene/SceneIDs.h` — NodeID 类型定义
- `Utils/Math/Matrix.h` — float4x4 矩阵
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- `Scene/Camera/Camera.h` — 相机类继承此基类
- `Scene/Lights/Light.h` — 灯光类继承此基类
- `Scene/Animation/AnimationController.h` — 动画控制器驱动此类对象

## 实现细节

- `.cpp` 文件仅包含 Python 绑定代码
- Python 绑定暴露 `hasAnimation`（只读）和 `animated`（读写）属性
- `updateFromAnimation` 为纯虚函数，子类必须实现具体的变换应用逻辑
