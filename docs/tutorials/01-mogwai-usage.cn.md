### [Index](../index.md) | [教程](./index.md) | Mogwai 使用指南

--------

# Mogwai 使用指南

## 构建 Mogwai

在 Visual Studio 中：
1. 配置 Visual Studio 解决方案（参见主 README）。
2. 从顶部菜单栏选择 `Build` -> `Build Solution`，等待编译完成。
3. 可执行文件位于 `build/<preset name>/bin/[Debug|Release]/Mogwai.exe`

## 运行 Mogwai

在 Visual Studio 中运行 Mogwai（按 Ctrl+F5），或通过命令行使用以下命令：

```
  Mogwai {OPTIONS}

    Mogwai render application.

  OPTIONS:

      -h, --help                        Display this help menu.
      -d[d3d12|vulkan],
      --device-type=[d3d12|vulkan]      Graphics device type.
      --list-gpus                       List available GPUs
      --gpu=[index]                     Select specific GPU to use
      --headless                        Start without opening a window and
                                        handling user input.
      -s[path], --script=[path]         Python script file to run.
      --deferred                        The script is loaded deferred.
      -S[path], --scene=[path]          Scene file (for example, a .pyscene
                                        file) to open.
      --shadercache=[shadercache]       Path to the GFX shader cache.
      -l[path], --logfile=[path]        File to write log into.
      -v[verbosity],
      --verbosity=[verbosity]           Logging verbosity (0=disabled, 1=fatal
                                        errors, 2=errors, 3=warnings, 4=infos,
                                        5=debugging)
      --silent                          Start without opening a window and
                                        handling user input (deprecated: use
                                        --headless).
      --width=[pixels]                  Initial window width.
      --height=[pixels]                 Initial window height.
      -c, --use-cache                   Use scene cache to improve scene load
                                        times.
      --rebuild-cache                   Rebuild the scene cache.
      --debug-shaders                   Generate shader debug info.
      --enable-debug-layer              Enable debug layer (enabled by default
                                        in Debug build).
      --precise                         Force all slang programs to run in
                                        precise mode
```

配合 `--silent` 和 `--script` 使用可以让 Mogwai 在后台进行渲染。

如果启动时未指定任何选项，Mogwai 将以空白画面启动。

## 加载脚本和资源

Mogwai 启动并运行后，接下来我们将加载一些内容。你可以加载两种类型的文件：脚本（通常包含一些全局设置和 render graph）和场景。

### 加载脚本（.py）
通过 `File -> Load Script` 或按 `Ctrl + O` 打开加载脚本对话框。导航到要运行的脚本位置并选择它以加载和运行。你也可以将脚本拖放到 Mogwai 中。请注意，用于 Mogwai 的脚本必须使用 Python 编写。完整的脚本文档可在[此处](../usage/scripting.md)找到。

在本教程中，我们将加载 Forward Renderer，位于 `Source/Mogwai/Data/ForwardRenderer.py`。

### 加载场景
Mogwai 会加载脚本中指定的场景（如果有的话）。如果脚本未加载场景，或者你想加载其他场景，可以通过 `File` -> `Load Scene` 或按 `Ctrl + Shift + O` 打开加载场景对话框。导航到要加载的场景文件位置并选择它。你也可以将场景文件拖放到 Mogwai 中。

示例中包含了一个 Arcade 场景，位于 `media/Arcade/Arcade.pyscene`。

## Mogwai 界面
加载脚本（以及可选的场景）后，你应该会看到类似以下的画面：

![Mogwai UI](./images/mogwai-ui.png)

主要界面元素：

1. 主菜单 - 值得注意的是，`File` 下的 `Save Config` 会将当前的 graph 和应用设置保存为脚本。此外，主菜单通常包含各种界面元素的开关以及控制帮助窗口。
2. Graph 界面 - 在这里你可以切换活动的 graph，以及修改场景和 pass 的特定设置。你也可以在此选择要显示的 pass 输出。所有更改都会实时反映。
3. 时间界面 - 这些控件允许你控制场景所在的时间/帧。暂停将暂停场景中的所有动画。请注意，渲染器不受影响，需要通过键盘快捷键手动暂停。
4. 性能分析器 - 仅在启用性能分析器时显示。
5. 控制台 - 默认隐藏，允许你通过 Python 脚本实时编辑当前活动 graph 的设置。

## Mogwai 控制键

以下是可用的控制键列表：

`F1` - 显示帮助信息\
`F2` - 显示/隐藏 GUI\
`F6` - 显示/隐藏 graph 控件\
`F9` - 显示/隐藏时间控件\
`F10` - 显示/隐藏 FPS\
`F11` - 显示/隐藏菜单栏\
`` ` `` - 显示/隐藏脚本控制台\
`P` - 启用/禁用性能分析\
`Esc` - 退出

`Ctrl + O` - 加载脚本\
`Ctrl + Shift + O` - 加载场景\
`F5` - 重新加载 shader\
`V` - 切换垂直同步\
`F12` - 截屏\
`Shift + F12` - 录制视频\
`Pause` 或 `Space` - 暂停/恢复全局计时器\
`Ctrl + Pause` 或 `Ctrl + Space` - 暂停/恢复渲染器

`W, A, S, D, Q, E` - 移动摄像机\
`Left Click + Drag` - 旋转摄像机\
`Shift` - 按住时加速摄像机移动\
`Ctrl` - 按住时减速摄像机移动\
`Z` - 放大当前悬停区域\
`Mouse Wheel` - 放大像素时更改缩放级别
