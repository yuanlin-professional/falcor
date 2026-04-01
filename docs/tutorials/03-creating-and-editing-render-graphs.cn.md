### [Index](../index.md) | [教程](./index.md) | 创建和编辑 Render Graph

--------

# 创建和编辑 Render Graph

现在我们已经编写了一个简单的 render pass，接下来让我们关注 render graph 的创建和编辑。有两种方法可以实现：使用 Render Graph Editor 和使用 Python 脚本。本教程将涵盖这两种方法，但建议使用编辑器。

## Render Graph Editor
将 `RenderGraphEditor` 设置为启动项目，然后像启动 Mogwai 一样启动它。你将看到类似以下的画面：

![RenderGraphEditor](./images/render-graph-editor.png)

主要界面元素：

1. Graph 编辑器 - 主工作区。你将在此组装 pass 以创建 graph。
2. Graph 编辑器设置 - 允许你选择不同的 graph 进行查看和编辑。值得注意的是，`Open in Mogwai` 按钮可以让你直接从编辑器在 Mogwai 中运行你的 graph。
3. Render Pass 列表 - 所有可用的 render pass 都在此处。只需拖放到 graph 编辑器中即可添加。
4. Render Pass 界面 - 当在编辑器窗口中选择了一个 render pass 时，其可通过界面控制的设置将显示在此处。

### 创建 Graph
使用编辑器创建 graph 的一般流程如下：

1. 将 render pass 从 Render Pass 窗口拖到 graph 编辑器面板中。
2. 用边连接 pass 节点。边有两种类型：
    - **数据依赖** - 将一个 pass 的输出资源连接到另一个 pass 的输入资源的边。表示为两个不同 pass 的输入/输出引脚之间的连线。
    - **执行依赖** - 可选的边，通常用于更精确地控制 graph 的执行顺序，或强制某个 pass 运行而不管其对 graph 输出的影响。在 graph 编辑器中，这些边连接两个不同 pass 的执行引脚（每个 pass 的未标记引脚）。
3. 通过悬停在相应引脚上并在弹出窗口中勾选 `Graph Output` 来标记输出。所有 render graph 都必须至少有一个输出。

作为示例，我们将使用上一教程中编写的 `ExampleBlitPass` render pass 来创建一个 graph。首先，将 `ImageLoader` 和 `ExampleBlitPass` 拖到 graph 编辑器面板中，然后通过点击 `ImageLoader` 并在 Render UI 窗口中选择 `Load Image` 来为其设置图片。接着，通过点击圆圈并拖动来将 `ImageLoader` 标记为 `dst` 的输出与 `ExampleBlitPass` 标记为 `input` 的输入用边连接起来（请确保点击外半部分，因为点击内半部分会移动 pass 的方块）。最后，将鼠标悬停在 `ExampleBlitPass` 的 `output` 圆圈上，勾选 `Graph Output` 框，将其标记为此 graph 的输出。你的 graph 应如下所示：

![ExampleBlitPassGraph](./images/example-blit-pass-graph.png)

点击 `Open in Mogwai` 将启动 Mogwai 并自动加载当前活动的 graph。如果一切操作正确，你应该会看到在 `ImageLoader` 中加载的图片显示在屏幕上。

### 实时编辑 Graph
你还可以使用 Render Graph Editor 直接编辑 graph 并让更改实时反映。要执行此操作，首先在 Mogwai 中启动 graph 的脚本。点击选择活动 graph 下拉菜单下方的 `Edit` 按钮，这将启动 Render Graph Editor 并默认加载当前活动的 graph。以下是我们上面创建的 graph 及其在 Mogwai 中的渲染效果：

![ExampleBlitPassWithRenderer](./images/example-blit-pass-with-renderer.png)

在这里，你可以通过点击 pass 来修改其属性；其选项将显示在右侧的 Render UI 窗口中。在大多数情况下，只要 graph 和选定的输出保持有效，任何更改都会立即反映在 Mogwai 中。如果你要移除 pass，请确保在进行任何更改之前先在 Mogwai 的 Graph UI 中选择一个之后仍然有效的输出。另一方面，如果你要添加 pass 并更改 graph 的输出，请注意你需要在 Mogwai 中手动选择新的输出。

作为示例，我们将通过移除 `ExampleBlitPass` 并添加 `ToneMapper` 来修改我们的 graph。由于我们要移除 `ExampleBlitPass`，请在从 graph 中移除 `ExampleBlitPass` 之前，选择 `List all outputs` 并在 Mogwai 中将当前输出设置为 `ImageLoader.dst`。然后，将 `ToneMapper` pass 从 Render Pass 窗口拖到 graph 编辑器面板中。将 `ImageLoader` 的 `dst` 字段连接到 `ToneMapper` 的 `src` 字段，并将 `ToneMapper` 的 `dst` 字段设置为 graph 输出。从输出下拉菜单中选择 `ToneMapper.dst` 或取消勾选 `List all outputs`，你应该会看到类似以下的画面：

![GraphEdits](./images/graph-edits.png)

更改任何 pass 特定的属性将实时反映在渲染器中。

## Python 脚本
通过脚本创建 render graph 与通过 Render Graph Editor 创建的步骤类似。以下是创建 render graph 时会用到的几个主要函数：
- `RenderGraph()` 接受一个字符串名称作为输入，并创建一个以该名称命名的空 render graph。
- `RenderPass()` 接受要构造的 pass 名称作为输入，并创建相应的 pass。它还可以接受一个字典来初始化 pass。
- `addPass()` 是 `RenderGraph` 的方法，将使用 `RenderPass()` 创建的 render pass 实例添加到 graph 中，并接受一个用户定义的名称作为此新 pass/节点的名称。
- `addEdge()` 将一个 pass 的输出连接到另一个 pass 的输入，接受两个字符串来指示源和目标。这些字符串的格式因添加的边类型不同而不同：
    - 数据依赖：字符串格式为 `"renderPassName.resourceName"`，其中 `renderPassName` 是 pass 的用户定义名称，`resourceName` 是 pass 反射中赋予资源的名称。
    - 执行依赖：指示源和目标 pass 的字符串是两个 pass 的用户定义名称。例如：`g.addEdge("RenderPassA", "RenderPassB")`。
- `markOutput()` 将 pass 的输出资源标记为整个 render graph 的期望输出。Render graph 必须始终有一个输出。输入字符串可以按以下格式：
    - `"renderPassName.resourceName"` 标记单个输出。
    - `"renderPassName"` 将该 pass 的所有输出标记为 graph 输出。
    - `"*"` 将所有 pass 的输出标记为 graph 输出。
    - `markOutput()` 接受一个可选的 `mask` 参数，指定帧捕获时要输出的颜色通道。
    - 可以使用不同的 mask 多次调用 `markOutput()`，帧捕获将生成单独的输出文件。

*有关脚本 API 的更多信息，可以在[此处](../usage/scripting.md)找到文档。*

通过脚本创建 render graph 的步骤：
1. 创建一个函数来包含命令。这不是必需的，但建议这样做。
2. 创建新的 graph，并创建所需的 pass。
3. 添加边以连接 pass。
4. 标记一个或多个 graph 输出。
5. 通过调用 `m.addGraph()` 将组装好的 graph 添加到 Mogwai。

我们上面使用编辑器创建的包含 `ExampleBlitPass` 的 graph 在脚本中如下所示：

```python
def render_graph_ExampleBlitPass():
    g = RenderGraph("ExampleBlitPass Example")
    ExampleBlitPass = createPass("ExampleBlitPass")
    g.addPass(ExampleBlitPass, "MyBlitPass")
    ImageLoader = createPass("ImageLoader", {'filename': 'Cubemaps\\Sorsele3\\posz.jpg',
                             'mips': False, 'srgb': True, 'arrayIndex': 0, 'mipLevel': 0})
    g.addPass(ImageLoader, "ImageLoader")
    g.addEdge("ImageLoader.dst", "MyBlitPass.input")
    g.markOutput("MyBlitPass.output")
    return g

try: m.addGraph(render_graph_ExampleBlitPass())
except NameError: None
```

你现在可以在 Mogwai 中运行此脚本，应该会看到与之前相同的结果。
