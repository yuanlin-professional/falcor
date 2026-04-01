# ui_demo.py 脚本文档

> 路径: `scripts/python/ui/ui_demo.py`
> 类型: Python 脚本
> 模块: scripts/python/ui

## 功能概述
该脚本是 Falcor Python UI 系统的完整演示。它展示了所有可用的 UI 控件类型（文本、按钮、复选框、下拉框、进度条、拖拽控件、滑块等），以及窗口管理功能（显示/关闭/移动/调整大小）。同时演示了回调函数、定时器和多窗口交互等高级用法。

## 主要功能/类

### `class WidgetWindow`
控件展示窗口，演示所有可用的 UI 控件。

#### `__init__(self, screen)`
| 参数 | 类型 | 说明 |
|------|------|------|
| screen | `ui.Screen` | UI 屏幕对象 |

创建包含以下控件组的窗口：
- **基础控件组**：Text、Button、Checkbox、Combobox、ProgressBar
- **拖拽控件组**：DragInt/2/3/4、DragFloat/2/3/4
- **滑块控件组**：SliderInt/2/3/4、SliderFloat/2/3/4
- **空组**：演示空 Group
- 启用/禁用所有控件的按钮

#### `set_widgets_enabled(self, enabled)`
| 参数 | 类型 | 说明 |
|------|------|------|
| enabled | `bool` | 是否启用控件 |

### `class DemoWindow`
控制窗口，演示窗口管理和交互功能。

#### `__init__(self, screen, widget_window)`
| 参数 | 类型 | 说明 |
|------|------|------|
| screen | `ui.Screen` | UI 屏幕对象 |
| widget_window | `WidgetWindow` | 控件展示窗口引用 |

提供以下功能：
- 显示/关闭控件窗口
- 移动/调整当前窗口大小
- 计数按钮（动态更新标签）
- 开始/停止后台任务（带进度条）

#### `count(self)`
按钮点击计数器。

#### `start_stop(self)`
启动/停止后台定时任务，更新进度条。

#### `timer_callback(self)`
定时器回调，每 50ms 更新进度条。

## 使用示例
```bash
python ui_demo.py
# 打开窗口显示 UI 控件演示
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架
- `falcor.ui` - Falcor UI 模块
- `threading` - 线程定时器

## 实现细节
- 所有控件都通过 `change_callback` 参数注册值变化回调
- WidgetWindow 位于 (900, 10)，大小 500x1000
- DemoWindow 位于 (300, 10)，大小 500x1000
- 测试平台分辨率为 1920x1080
- 后台任务使用 `threading.Timer` 实现，每 50ms 递增进度条 1%
- 使用逐帧循环（`while not testbed.should_close`）而非 `testbed.run()`，以支持 Python 定时器工作
- 控件的 `enabled` 属性可以动态切换
- 按钮的 `callback` 可以在创建后通过属性赋值设置
