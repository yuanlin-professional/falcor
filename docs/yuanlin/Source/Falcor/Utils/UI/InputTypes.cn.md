# InputTypes 源码文档

> 路径: `Source/Falcor/Utils/UI/InputTypes.h`
> 类型: C++ 头文件
> 模块: Utils/UI

## 功能概述

InputTypes.h 定义了 Falcor 框架中所有输入相关的枚举类型和事件结构体，包括键盘按键枚举、鼠标按钮枚举、修饰键标志，以及鼠标事件、键盘事件和游戏手柄事件的数据结构。它是整个输入系统的类型基础。

## 类与接口

### 命名空间 `Input`

#### `Input::MouseButton`

鼠标按钮枚举。

| 值 | 说明 |
|----|------|
| `Left` | 左键 |
| `Middle` | 中键 |
| `Right` | 右键 |
| `Count` | 按钮总数 |

#### `Input::ModifierFlags`

修饰键标志位（可按位组合）。

| 值 | 数值 | 说明 |
|----|------|------|
| `None` | 0 | 无修饰键 |
| `Shift` | 1 | Shift 键 |
| `Ctrl` | 2 | Ctrl 键 |
| `Alt` | 4 | Alt 键 |

#### `Input::Modifier`

修饰键枚举（用于 InputState 查询）。

| 值 | 说明 |
|----|------|
| `Shift` | Shift 键 |
| `Ctrl` | Ctrl 键 |
| `Alt` | Alt 键 |

#### `Input::Key`

键盘按键枚举，ASCII 字符使用其大写 ASCII 码值（0-255 保留），特殊键从 256 开始。

| 分类 | 代表值 | 说明 |
|------|--------|------|
| ASCII 字符 | `Space`、`A`-`Z`、`Key0`-`Key9` 等 | 使用 ASCII 码值 |
| 特殊键 | `Escape`(256)、`Tab`、`Enter`、`Backspace` 等 | 从 256 开始递增 |
| 功能键 | `F1`-`F12` | 功能键 |
| 小键盘 | `Keypad0`-`Keypad9`、`KeypadAdd` 等 | 数字小键盘 |
| 修饰键 | `LeftShift`、`RightShift`、`LeftControl` 等 | 左右各有独立键值 |
| 其他 | `Unknown`、`Count` | 未知键和键总数 |

### `MouseEvent`

鼠标事件结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `type` | `MouseEvent::Type` | 事件类型：`ButtonDown`、`ButtonUp`、`Move`、`Wheel` |
| `pos` | `float2` | 归一化坐标 [0,1]，(0,0) 为窗口左上角 |
| `screenPos` | `float2` | 屏幕空间坐标 [0, clientSize] |
| `wheelDelta` | `float2` | 滚轮变化量（仅 Wheel 事件有效） |
| `mods` | `Input::ModifierFlags` | 修饰键标志（仅按钮事件有效） |
| `button` | `Input::MouseButton` | 触发的鼠标按钮（仅 ButtonDown/Up 有效） |

### `KeyboardEvent`

键盘事件结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `type` | `KeyboardEvent::Type` | 事件类型：`KeyPressed`、`KeyReleased`、`KeyRepeated`、`Input` |
| `key` | `Input::Key` | 按键值 |
| `mods` | `Input::ModifierFlags` | 修饰键标志 |
| `codepoint` | `uint32_t` | UTF-32 码点（仅 Input 类型有效，来自 GLFW） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool hasModifier(Input::Modifier mod) const` | 检查事件是否携带指定修饰键 |

### `GamepadButton`

游戏手柄按钮枚举。

| 值 | 说明 |
|----|------|
| `A`、`B`、`X`、`Y` | 面板按钮 |
| `LeftBumper`、`RightBumper` | 肩键 |
| `Back`、`Start`、`Guide` | 功能键 |
| `LeftThumb`、`RightThumb` | 摇杆按下 |
| `Up`、`Right`、`Down`、`Left` | 方向键 |

### `GamepadEvent`

游戏手柄事件结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `type` | `GamepadEvent::Type` | 事件类型：`ButtonDown`、`ButtonUp`、`Connected`、`Disconnected` |
| `button` | `GamepadButton` | 按钮值 |

### `GamepadState`

游戏手柄完整状态。

| 成员 | 类型 | 说明 |
|------|------|------|
| `leftX` / `leftY` | `float` | 左摇杆轴值 |
| `rightX` / `rightY` | `float` | 右摇杆轴值 |
| `leftTrigger` / `rightTrigger` | `float` | 左右扳机值 |
| `buttons` | `std::bitset` | 所有按钮状态位集 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool isButtonDown(GamepadButton button) const` | 检查手柄按钮是否按下 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏（`FALCOR_ENUM_CLASS_OPERATORS`）
- `Utils/Math/Vector.h` — `float2` 类型

### 被以下文件引用

- `Utils/UI/InputState.h` — 使用 `Input::Key`、`Input::MouseButton`、`Input::Modifier` 枚举
- `Utils/UI/Gui.cpp` — 鼠标/键盘事件处理
- `Utils/UI/PixelZoom.cpp` — 键盘和鼠标事件处理
- Falcor 框架中所有处理输入事件的代码

## 实现细节

- `Input::Key` 枚举设计巧妙：ASCII 可打印字符直接使用其 ASCII 码值，特殊键从 256 开始，避免冲突
- `ModifierFlags` 支持按位组合（通过 `FALCOR_ENUM_CLASS_OPERATORS` 宏），`Modifier` 枚举值与对应的 `ModifierFlags` 值相同，便于互相转换
- `MouseEvent` 同时提供归一化坐标和屏幕空间坐标，方便不同使用场景
- `KeyboardEvent` 的 `Input` 类型专门用于处理文本输入，提供 UTF-32 码点
