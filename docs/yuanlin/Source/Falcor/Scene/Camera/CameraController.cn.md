# CameraController 源码文档

> 路径: `Source/Falcor/Scene/Camera/CameraController.h` + `.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Camera

## 功能概述

实现了相机控制器体系，包括基类接口和三种具体控制器：轨道相机控制器（Orbiter）、第一人称相机控制器（FPS）和六自由度相机控制器（6DoF）。控制器处理鼠标、键盘和手柄输入，驱动相机的位置和朝向更新。

## 类与接口

### `CameraController`（基类）

- **继承**: 无（独立基类）
- **职责**: 定义相机控制器的通用接口，管理速度和世界上方向

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual bool onMouseEvent(const MouseEvent&)` | 处理鼠标事件 |
| `virtual bool onKeyEvent(const KeyboardEvent&)` | 处理键盘事件 |
| `virtual bool onGamepadState(const GamepadState&)` | 处理手柄状态 |
| `virtual bool update() = 0` | 更新相机（纯虚函数） |
| `void setUpDirection(UpDirection)` | 设置世界上方向 |
| `void setCameraSpeed(float)` | 设置移动速度（世界单位/秒） |
| `void setCameraBounds(const AABB&)` | 设置相机位置边界 |
| `virtual void resetInputState()` | 重置输入状态 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpCamera` | `ref<Camera>` | 被控制的相机对象 |
| `mUpDirection` | `UpDirection` | 世界上方向（默认 Y 正方向） |
| `mSpeed` | `float` | 移动速度 |
| `mBounds` | `AABB` | 位置约束边界 |

### `OrbiterCameraController`

- **继承**: `CameraController`
- **职责**: 围绕指定中心点旋转的轨道相机控制

#### 操作方式

- 左键拖拽：轨道旋转
- 鼠标滚轮：缩放距离

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setModelParams(const float3& center, float radius, float distanceInRadius)` | 设置模型中心、半径和初始距离 |
| `bool onMouseEvent(const MouseEvent&) override` | 处理鼠标输入 |
| `bool update() override` | 更新相机位置和朝向 |

### `FirstPersonCameraControllerCommon<b6DoF>`（模板类）

- **继承**: `CameraController`
- **职责**: 第一人称/六自由度相机控制

#### 操作方式

- W/S/A/D：前后左右移动
- Q/E：上下移动
- 左键拖拽：旋转视角
- 右键拖拽（仅 6DoF）：滚转
- Shift：加速（10 倍）
- Ctrl：减速（0.25 倍）
- 手柄左摇杆：移动
- 手柄右摇杆：旋转
- 手柄扳机：上下移动

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mMovement` | `bitset<6>` | 六方向移动状态 |
| `mSpeedModifier` | `float` | 速度修正因子 |
| `mTimer` | `CpuTimer` | 帧间时间差计算 |
| `mGamepadLeftStick/RightStick` | `float2` | 手柄摇杆值 |

### 类型别名

| 别名 | 实际类型 | 说明 |
|------|----------|------|
| `FirstPersonCameraController` | `FirstPersonCameraControllerCommon<false>` | 标准第一人称控制器 |
| `SixDoFCameraController` | `FirstPersonCameraControllerCommon<true>` | 六自由度控制器 |

## 依赖关系

### 本文件引用

- `Camera.h` — 相机类
- `Utils/UI/InputTypes.h` — 输入事件类型
- `Utils/Math/FalcorMath.h` — 数学辅助函数（`project2DCrdToUnitSphere`、`quatFromRotationBetweenVectors`）
- `Utils/Timing/CpuTimer.h` — CPU 计时器

### 被以下文件引用

- `Scene/Scene.h` — 场景管理中的相机控制

## 实现细节

- 轨道控制器使用 arcball 旋转，将 2D 鼠标坐标映射到单位球面
- 第一人称控制器的移动量与帧间时间成正比，最大帧时间限制为 0.1 秒以避免加载时跳跃
- 手柄输入应用了死区（0.1）和幂曲线（1.2 指数）处理
- 旋转通过四元数实现，先绕侧向轴旋转（俯仰），再绕上方向旋转（偏航）
- 位置可选地约束在 AABB 边界内
