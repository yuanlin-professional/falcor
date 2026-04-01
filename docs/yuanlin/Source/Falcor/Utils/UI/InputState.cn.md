# InputState 源码文档

> 路径: `Source/Falcor/Utils/UI/InputState.h` + `Source/Falcor/Utils/UI/InputState.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

InputState 类用于追踪当前帧和上一帧的键盘与鼠标输入状态。它提供了查询按键/鼠标按钮是否按下、刚按下或刚释放的接口，并支持修饰键（Shift、Ctrl、Alt）的状态查询。该类不直接消耗输入事件，用户需自行在回调中返回 `true` 来告知框架事件已处理。

## 类与接口

### `InputState`

- **继承**: 无
- **职责**: 维护键盘和鼠标按钮的逐帧状态，支持按下/释放/持续按住的查询

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool isMouseMoving() const` | 检查鼠标是否在当前帧移动 |
| `bool isKeyDown(Input::Key key) const` | 检查指定按键是否正在按住 |
| `bool isKeyPressed(Input::Key key) const` | 检查指定按键是否在本帧刚按下（上帧未按下，本帧按下） |
| `bool isKeyReleased(Input::Key key) const` | 检查指定按键是否在本帧刚释放（上帧按下，本帧未按下） |
| `bool isMouseButtonDown(Input::MouseButton mb) const` | 检查鼠标按钮是否正在按住 |
| `bool isMouseButtonClicked(Input::MouseButton mb) const` | 检查鼠标按钮是否在本帧刚点击 |
| `bool isMouseButtonReleased(Input::MouseButton mb) const` | 检查鼠标按钮是否在本帧刚释放 |
| `bool isModifierDown(Input::Modifier mod) const` | 检查修饰键是否正在按住 |
| `bool isModifierPressed(Input::Modifier mod) const` | 检查修饰键是否在本帧刚按下 |
| `bool isModifierReleased(Input::Modifier mod) const` | 检查修饰键是否在本帧刚释放 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void onKeyEvent(const KeyboardEvent& keyEvent)` | 处理键盘事件，更新内部按键状态 |
| `void onMouseEvent(const MouseEvent& mouseEvent)` | 处理鼠标事件，更新内部鼠标状态 |
| `void endFrame()` | 帧结束时将当前状态复制到上一帧状态，重置鼠标移动标志 |
| `bool getModifierState(const KeyStates& states, Input::Modifier mod) const` | 根据按键状态获取修饰键状态（检查左右两侧按键） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCurrentKeyState` | `std::bitset<kKeyCount>` | 当前帧按键状态 |
| `mPreviousKeyState` | `std::bitset<kKeyCount>` | 上一帧按键状态 |
| `mCurrentMouseState` | `std::bitset<kMouseButtonCount>` | 当前帧鼠标按钮状态 |
| `mPreviousMouseState` | `std::bitset<kMouseButtonCount>` | 上一帧鼠标按钮状态 |
| `mMouseMoving` | `bool` | 当前帧鼠标是否移动 |

## 依赖关系

### 本文件引用

- `Utils/UI/InputTypes.h` — 输入枚举类型（`Input::Key`、`Input::MouseButton`、`Input::Modifier`）和事件结构体
- `Core/Macros.h` — Falcor 宏
- `Core/Error.h` — `FALCOR_UNREACHABLE()` 宏

### 被以下文件引用

- `SampleApp`（友元类）通过私有方法 `onKeyEvent`、`onMouseEvent`、`endFrame` 驱动状态更新

## 实现细节

- 使用 `std::bitset` 高效存储大量按键状态，按 `Input::Key` 枚举值作为索引
- 修饰键查询通过检查左右两侧的物理按键实现（如 Shift = LeftShift || RightShift）
- 每帧结束时通过 `endFrame()` 将当前状态滚动到"上一帧"，为下一帧的 pressed/released 判断提供基准
- `SampleApp` 作为友元类直接调用私有事件处理方法，保持了封装性
