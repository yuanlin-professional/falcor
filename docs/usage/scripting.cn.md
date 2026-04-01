### [Index](../index.md) | [Usage](./index.md) | 脚本

--------

**注意：** 本文档尚未更新，不再反映 Python API 的当前状态。

# Mogwai 脚本参考

## Sample API

#### 全局函数

| Function                           | Description                                                                   |
|------------------------------------|-------------------------------------------------------------------------------|
| `loadRenderPassLibrary(name)`      | 加载一个 render pass 库。                                                      |
| `createPass(name, dict)`           | 使用 `dict` 中的配置选项创建一个新的 render pass。                              |
| `renderFrame()`                    | 渲染一帧。如果时钟未暂停，将前进一个 tick。                                      |
| `setWindowPos(x, y)`               | 设置窗口位置（以像素为单位）。                                                   |
| `resizeFrameBuffer(width, height)` | 调整主帧缓冲区大小。                                                            |
| `resizeSwapChain(width, height)`   | 调整窗口/交换链大小。**已弃用**：请改用 `resizeFrameBuffer`。                    |
| `exit(errorCode=0)`                | 以给定的错误码终止应用程序。                                                     |

## Mogwai API

#### 全局函数

| Function       | Description                      |
|----------------|----------------------------------|
| `help()`       | 打印全局帮助信息。                |
| `help(object)` | 打印特定对象的帮助信息。          |

#### 全局变量

| Variable | Description                                                                 |
|----------|-----------------------------------------------------------------------------|
| `m`      | `Renderer` 的实例。                                                         |
| `t`      | `Clock` 的实例。**已弃用**：请改用 `m.clock`。                               |
| `fc`     | `FrameCapture` 的实例。**已弃用**：请改用 `m.frameCapture`。                 |
| `vc`     | `VideoCapture` 的实例。**已弃用**：请改用 `m.videoCapture`。                 |
| `tc`     | `TimingCapture` 的实例。**已弃用**：请改用 `m.timingCapture`。               |

#### Renderer

class falcor.**Renderer**

| Property        | Type            | Description                     |
|-----------------|-----------------|---------------------------------|
| `scene`         | `Scene`         | 当前活动场景（只读）。           |
| `activeGraph`   | `RenderGraph`   | 当前活动 render graph（只读）。  |
| `ui`            | `bool`          | 显示/隐藏 UI。                  |
| `clock`         | `Clock`         | 时钟。                          |
| `profiler`      | `Profiler`      | 性能分析器。                     |
| `frameCapture`  | `FrameCapture`  | 帧捕获。                        |
| `videoCapture`  | `VideoCapture`  | 视频捕获。                      |
| `timingCapture` | `TimingCapture` | 计时捕获。                      |

| Method                                                  | Description                                                                   |
|---------------------------------------------------------|-------------------------------------------------------------------------------|
| `script(path)`                                          | 运行脚本。                                                                     |
| `loadScene(path, buildFlags=SceneBuilderFlags.Default)` | 加载场景。可用的构建标志见下文。                                                 |
| `unloadScene()`                                         | 显式卸载场景以释放内存。                                                        |
| `saveConfig(path)`                                      | 将当前状态保存到配置文件。                                                      |
| `addGraph(graph)`                                       | 添加一个 render graph。                                                        |
| `removeGraph(graph)`                                    | 移除一个 render graph。`graph` 可以是 render graph 对象或名称。                 |
| `getGraph(name)`                                        | 按名称获取 render graph。                                                      |
| `resizeFrameBuffer(width, height)`                      | 调整主帧缓冲区大小。                                                            |
| `resizeSwapChain(width, height)`                        | 调整窗口/交换链大小。**已弃用**：请改用 `resizeFrameBuffer`。                    |

#### Clock

class falcor.**Clock**

| Property    | Type    | Description                                                                     |
|-------------|---------|---------------------------------------------------------------------------------|
| `time`      | `float` | 当前时间（单位：_秒_）。                                                         |
| `frame`     | `int`   | 当前帧编号。                                                                     |
| `framerate` | `int`   | 帧率（单位：_帧/秒_）。                                                          |
| `timeScale` | `float` | 时间缩放因子。                                                                   |
| `exitTime`  | `float` | 终止应用程序的时间（单位：_秒_）。设置为 `0` 以禁用。                             |
| `exitFrame` | `int`   | 终止应用程序的帧编号。设置为 `0` 以禁用。                                         |

| Method           | Description                       |
|------------------|-----------------------------------|
| `pause()`        | 暂停时钟。                         |
| `play()`         | 恢复时钟。                         |
| `stop()`         | 停止时钟（暂停并重置）。            |
| `step(frames=1)` | 向前或向后步进。                    |

#### Profiler

class falcor.**Profiler**

| Property      | Type   | Description                               |
|---------------|--------|-------------------------------------------|
| `enabled`     | `bool` | 启用/禁用性能分析器。                      |
| `paused`      | `bool` | 暂停/恢复性能分析器。                      |
| `isCapturing` | `bool` | 如果性能分析器正在捕获则为 True（只读）。   |
| `events`      | `dict` | 性能分析事件（只读）。                      |

| Method           | Description                              |
|------------------|------------------------------------------|
| `startCapture()` | 开始捕获。                                |
| `endCapture()`   | 结束捕获。返回捕获的数据。                 |

##### Profiler 事件名称

性能分析器事件名称由于其层级性质，以路径形式表示。例如，`/present` 是一个顶层事件，对应呈现最终图像所花费的时间，而 `/onFrameRender/RenderGraphExe::execute()` 是一个嵌套事件，对应执行 render graph 所花费的时间。由于性能分析器事件同时测量 CPU 和 GPU 时间，路径会扩展 `/gpuTime` 或 `/cpuTime` 后缀以唯一标识实际测量的量。例如，`/present/gpuTime` 表示呈现最终图像的 GPU 时间。

##### 访问性能分析器数据

性能分析器事件数据可以通过 `m.profiler.events` 访问。这将返回一个字典，包含上一帧的所有事件数据，使用事件名称作为键。每个条目本身也是一个字典，包含以下键/值：

| Key       | Value                                                        |
|-----------|--------------------------------------------------------------|
| `name`    | 事件名称（与访问此条目的键相同）。                             |
| `value`   | 最后一次测量值（单位：_毫秒_）。                               |
| `average` | 指数移动平均值（单位：_毫秒_）。                               |
| `stats`   | 包含最近 512 帧统计数据的字典。                                |

`stats` 字典包含以下键/值：

| Key      | Value                           |
|----------|---------------------------------|
| `mean`   | 平均值（单位：_毫秒_）。        |
| `stdDev` | 标准差（单位：_毫秒_）。        |
| `min`    | 最小值（单位：_毫秒_）。        |
| `max`    | 最大值（单位：_毫秒_）。        |

要获取当前呈现的 GPU 时间，可以使用 `m.profiler.events["/present/gpuTime"]["value"]`。要获取最近 512 帧的平均值，可以使用 `m.profiler.events["/present/"gpuTime"]["stats"]["mean"]`。

##### 捕获性能分析器数据

要从多帧中捕获性能分析器数据，可以使用单独的捕获 API。使用 `m.profiler.startCapture()` 开始捕获。性能数据将被内部捕获，直到调用 `m.profiler.endCapture()`，该方法将返回一个包含所有捕获数据的字典。

捕获字典包含以下键/值：

| Key          | Value                                          |
|--------------|------------------------------------------------|
| `frameCount` | 捕获的总帧数。                                  |
| `events`     | 包含捕获的事件数据的字典。                       |

`events` 字典使用事件名称作为键。每个条目本身也是一个字典，包含以下键/值：

| Key       | Value                                                        |
|-----------|--------------------------------------------------------------|
| `name`    | 事件名称（与访问此条目的键相同）。                             |
| `records` | 包含每帧原始数据的数组（单位：_毫秒_）。                       |
| `stats`   | 包含从原始帧数据计算的统计数据的字典。                         |

`stats` 字典的结构与上述相同，但基于捕获的数据而非最近 512 帧进行计算。

以下代码片段展示了如何在 256 帧中捕获性能数据并打印平均 GPU 帧渲染时间：

```python
m.profiler.enabled = True
m.profiler.startCapture()
for frame in range(256):
    m.renderFrame()
capture = m.profiler.endCapture()
m.profiler.enabled = False

meanFrameTime = capture["events"]["/onFrameRender/gpuTime"]["stats"]["mean"]
print(f"Mean frame time: {}", meanFrameTime)
```

#### FrameCapture

帧捕获始终会转储标记的 graph 输出。你可以使用 `graph.markOutput()` 和 `graph.unmarkOutput()` 来控制要转储的输出。

帧计数器在 Falcor 中从零开始（在 Maya 中默认从一开始，因此帧编号可能偏移一帧）。

默认情况下，捕获的帧存储在可执行文件目录中。可以通过设置 `outputDir` 来更改。

**注意：** 当时间暂停时，帧计数器不会前进。如果你在时间暂停时捕获，每渲染一帧都会覆盖已捕获的帧。解决方法是在每次使用 `fc.capture()` 捕获之间更改基础文件名，参见下方示例。

class falcor.**FrameCapture**

| Property       | Type   | Description                                                                  |
|----------------|--------|------------------------------------------------------------------------------|
| `outputDir`    | `str`  | 捕获输出目录。                                                                |
| `baseFilename` | `str`  | 捕获基础文件名。帧 ID 和输出名称将附加到此文件名之后。                          |
| `ui`           | `bool` | 显示/隐藏 UI。                                                               |

| Method                     | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| `reset(graph)`             | 重置给定 graph 的帧捕获（如果设置为 `None` 则重置所有 graph）。               |
| `capture()`                | 捕获当前帧。                                                                 |
| `addFrames(graph, frames)` | 为给定 graph 添加要捕获的帧列表。                                             |
| `print()`                  | 打印所有可用 graph 的请求捕获帧。                                             |
| `print(graph)`             | 打印指定 graph 的请求捕获帧。                                                |

**示例：** *在时钟运行时捕获帧列表，然后退出*
```python
m.clock.exitFrame = 101
m.frameCapture.outputDir = "../../../Output"
m.frameCapture.baseFilename = "Mogwai"
m.frameCapture.addFrames(m.activeGraph, [20, 50, 100])
```

**示例：** *在时钟暂停时捕获帧，然后退出*
```python
m.clock.pause()
m.frameCapture.outputDir = "../../../Output"

frames = [20, 50, 100]
for i in range(101):
    renderFrame()
    if i in frames:
        m.frameCapture.baseFilename = f"Mogwai-{i:04d}"
        m.frameCapture.capture()
exit()
```


#### VideoCapture

视频捕获始终会捕获标记的 graph 输出。你可以使用 `graph.markOutput()` 和 `graph.unmarkOutput()` 来控制要转储的输出。

enum falcor.**Codec**

`Raw`, `H264`, `HVEC`, `MPEG2`, `MPEG4`

class falcor.**VideoCapture**

| Property       | Type    | Description                                                      |
|----------------|---------|------------------------------------------------------------------|
| `outputDir`    | `str`   | 捕获输出目录。                                                    |
| `baseFilename` | `str`   | 捕获基础文件名。输出名称将附加到此文件名之后。                      |
| `ui`           | `bool`  | 显示/隐藏 UI。                                                   |
| `codec`        | `Codec` | 视频编解码器（`Raw`, `H264`, `HVEC`, `MPEG2`, `MPEG4`）。         |
| `fps`          | `int`   | 视频帧率。                                                        |
| `bitrate`      | `float` | 视频码率（单位：Mpbs）。                                           |
| `gopSize`      | `int`   | 视频 GOP 大小。                                                   |

| Method                     | Description                                                                                           |
|----------------------------|-------------------------------------------------------------------------------------------------------|
| `reset(graph)`             | 重置给定 graph 的视频捕获（如果设置为 `None` 则重置所有 graph）。                                       |
| `addRanges(graph, ranges)` | 为给定 graph 添加要捕获的帧范围列表。`ranges` 是一个 `(start, end)` 元组的列表。                         |
| `print()`                  | 打印所有可用 graph 的请求捕获范围。                                                                     |
| `print(graph)`             | 打印指定 graph 的请求捕获范围。                                                                        |

示例：
```python
# Video Capture
m.videoCapture.outputDir = "."
m.videoCapture.baseFilename = "Mogwai"
m.videoCapture.codec = Codec.H264
m.videoCapture.fps = 60
m.videoCapture.bitrate = 4.0
m.videoCapture.gopSize = 10
m.videoCapture.addRanges(m.activeGraph, [[30, 300]])
```

#### TimingCapture

class falcor.**TimingCapture**

| Method                   | Description                                       |
|--------------------------|---------------------------------------------------|
| `captureFrameTime(path)` | 开始将帧时间写入给定的文件路径。                    |

示例：
```python
# Timing Capture
m.timingCapture.captureFrameTime("timecapture.csv")
```

### Core API

module **falcor**

#### 全局函数

| Function                      | Description                 |
|-------------------------------|-----------------------------|
| `loadRenderPassLibrary(name)` | 加载一个 render pass 库。    |
| `cls`                         | 清空控制台。                 |

#### ResourceFormat

enum falcor.**ResourceFormat**

`Unknown`, `R8Unorm`, `R8Snorm`, `R16Unorm`, `R16Snorm`, `RG8Unorm`, `RG8Snorm`, `RG16Unorm`, `RG16Snorm`, `RGB16Unorm`, `RGB16Snorm`, `R24UnormX8`, `RGB5A1Unorm`, `RGBA8Unorm`, `RGBA8Snorm`, `RGB10A2Unorm`, `RGB10A2Uint`, `RGBA16Unorm`, `RGBA8UnormSrgb`, `R16Float`, `RG16Float`, `RGB16Float`, `RGBA16Float`, `R32Float`, `R32FloatX32`, `RG32Float`, `RGB32Float`, `RGBA32Float`, `R11G11B10Float`, `RGB9E5Float`, `R8Int`, `R8Uint`, `R16Int`, `R16Uint`, `R32Int`, `R32Uint`, `RG8Int`, `RG8Uint`, `RG16Int`, `RG16Uint`, `RG32Int`, `RG32Uint`, `RGB16Int`, `RGB16Uint`, `RGB32Int`, `RGB32Uint`, `RGBA8Int`, `RGBA8Uint`, `RGBA16Int`, `RGBA16Uint`, `RGBA32Int`, `RGBA32Uint`, `BGRA8Unorm`, `BGRA8UnormSrgb`, `BGRX8Unorm`, `BGRX8UnormSrgb`, `Alpha8Unorm`, `Alpha32Float`, `R5G6B5Unorm`, `D32Float`, `D16Unorm`, `D32FloatS8X24`, `D24UnormS8`, `BC1Unorm`, `BC1UnormSrgb`, `BC2Unorm`, `BC2UnormSrgb`, `BC3Unorm`, `BC3UnormSrgb`, `BC4Unorm`, `BC4Snorm`, `BC5Unorm`, `BC5Snorm`, `BC6HS16`, `BC6HU16`, `BC7Unorm`, `BC7UnormSrgb`

#### RenderGraph

class falcor.**RenderGraph**

| Property | Type  | Description               |
|----------|-------|---------------------------|
| `name`   | `str` | render graph 的名称。      |

| Method                         | Description                                                                                  |
|--------------------------------|----------------------------------------------------------------------------------------------|
| `RenderGraph(name)`            | 创建一个新的 render graph。                                                                   |
| `addPass(pass, name)`          | 向 graph 中添加一个 render pass。                                                             |
| `removePass(name)`             | 从 graph 中移除一个 render pass。                                                             |
| `updatePass(name, dict)`       | 使用 `dict` 中的新配置选项更新 render pass。                                                   |
| `getPass(name)`                | 按名称获取 pass。                                                                             |
| `addEdge(src, dst)`            | 向 render graph 添加一条边。                                                                  |
| `removeEdge(src, dst)`         | 从 render graph 移除一条边。                                                                  |
| `markOutput(name, mask)`       | 将 render pass 输出标记为 graph 输出。`mask` 是可选的 `TextureChannelFlags` 枚举。              |
| `unmarkOutput(name)`           | 取消标记输出。                                                                                |
| `getOutput(index)`             | 按索引获取输出。                                                                              |
| `getOutput(name)`              | 按名称获取输出。                                                                              |

**注意：**
* `markOutput` 将输出标记为可在 Mogwai 中选择并用于帧捕获。第一个被标记的输出将成为 Mogwai 中的默认输出。
* 输出通过 `renderPass.outputName` 形式的名称标识。
* 该函数接受一个可选的 `mask` 参数，用于指定帧捕获时要捕获的颜色通道。如果未提供 mask，默认为 RGB 并忽略 alpha。
* 一个输出可以使用不同的 mask 多次标记。每个版本将由帧捕获写入单独的文件。

#### RenderPass

class falcor.**RenderPass**

| Property | Type  | Description                                |
|----------|-------|--------------------------------------------|
| `name`   | `str` | render pass 的名称（只读）。                |
| `type`   | `str` | render pass 的类型名称（只读）。            |
| `desc`   | `str` | render pass 的描述（只读）。                |

| Method            | Description                                                     |
|-------------------|-----------------------------------------------------------------|
| `getDictionary()` | 返回包含当前设置的配置字典。                                      |

#### Texture

class falcor.**Texture**

| Property    | Type             | Description                        |
|-------------|------------------|------------------------------------|
| `width`     | `int`            | 宽度（像素，只读）。                |
| `height`    | `int`            | 高度（像素，只读）。                |
| `depth`     | `int`            | 深度（像素/层数，只读）。           |
| `mipCount`  | `int`            | mip 级别数量（只读）。              |
| `arraySize` | `int`            | 数组大小（只读）。                  |
| `samples`   | `int`            | 采样数量（只读）。                  |
| `format`    | `ResourceFormat` | 纹理格式（只读）。                  |

| Method        | Description                                   |
|---------------|-----------------------------------------------|
| `data(index)` | 返回给定子资源索引的原始数据。                  |

#### AABB

class falcor.**AABB**

| Property   | Type     | Description                               |
|------------|----------|-------------------------------------------|
| `minPoint` | `float3` | 最小点。                                   |
| `maxPoint` | `float3` | 最大点。                                   |
| `valid`    | `bool`   | 如果 AABB 有效则为 True（只读）。           |
| `center`   | `float3` | 中心点（只读）。                            |
| `extent`   | `float3` | 范围（只读）。                              |
| `area`     | `float`  | 总面积（只读）。                            |
| `volume`   | `float`  | 总体积（只读）。                            |
| `radius`   | `float`  | 外接球半径（只读）。                        |

| Method            | Description                       |
|-------------------|-----------------------------------|
| `invalidate()`    | 使 AABB 无效。                     |
| `include(p)`      | 将一个点包含到 AABB 中。           |
| `include(b)`      | 将另一个 AABB 包含到 AABB 中。     |
| `intersection(b)` | 与另一个 AABB 求交。               |


### Scene API

#### SceneRenderSettings

class falcor.**SceneRenderSettings**

| Property            | Type   | Description                                   |
|---------------------|--------|-----------------------------------------------|
| `useEnvLight`       | `bool` | 启用/禁用环境贴图光照。                        |
| `useAnalyticLights` | `bool` | 启用/禁用解析光源光照。                        |
| `useEmissiveLights` | `bool` | 启用/禁用自发光光照。                          |
| `useGridVolumes`    | `bool` | 启用/禁用网格体积渲染。                        |

#### Scene

class falcor.**Scene**

| Property         | Type                    | Description                                                             |
|------------------|-------------------------|-------------------------------------------------------------------------|
| `stats`          | `dict`                  | 包含场景统计信息的字典。                                                  |
| `bounds`         | `AABB`                  | 世界空间场景边界（只读）。                                                |
| `animated`       | `bool`                  | 启用/禁用场景动画。                                                      |
| `loopAnimations` | `bool`                  | 启用/禁用全局循环场景动画。                                               |
| `renderSettings` | `SceneRenderSettings`   | 决定场景渲染方式的设置。                                                  |
| `updateCallback` | `function(scene, time)` | 在每帧开始时调用，用于程序化更新场景。                                     |
| `camera`         | `Camera`                | 相机。                                                                   |
| `cameraSpeed`    | `float`                 | 交互式相机的速度。                                                       |
| `envMap`         | `EnvMap`                | 环境贴图。                                                               |
| `animations`     | `list(Animation)`       | 动画列表。                                                               |
| `cameras`        | `list(Camera)`          | 相机列表。                                                               |
| `lights`         | `list(Light)`           | 光源列表。                                                               |
| `materials`      | `list(Material)`        | 材质列表。                                                               |
| `volumes`        | `list(Volume)`          | **已弃用**：请改用 `gridVolumes`。                                       |
| `gridVolumes`    | `list(GridVolume)`      | 网格体积列表。                                                           |

| Method                               | Description                                            |
|--------------------------------------|--------------------------------------------------------|
| `setEnvMap(path)`                    | 从图像加载环境贴图。                                    |
| `getLight(index)`                    | 按索引返回光源。                                        |
| `getLight(name)`                     | 按名称返回光源。                                        |
| `getMaterial(index)`                 | 按索引返回材质。                                        |
| `getMaterial(name)`                  | 按名称返回材质。                                        |
| `getVolume(index)`                   | **已弃用**：请改用 `getGridVolume`。                    |
| `getGridVolume(index)`               | 按索引返回网格体积。                                    |
| `getVolume(name)`                    | **已弃用**：请改用 `getGridVolume`。                    |
| `getGridVolume(name)`                | 按名称返回网格体积。                                    |
| `addViewpoint()`                     | 将当前相机视点添加到视点列表。                           |
| `addViewpoint(position, target, up)` | 将视点添加到视点列表。                                   |
| `removeViewpoint()`                  | 移除选中的视点。                                        |
| `selectViewpoint(index)`             | 选择特定视点并将相机移动到该位置。                       |

#### Camera

class falcor.**Camera**

| Property         | Type     | Description                                  |
|------------------|----------|----------------------------------------------|
| `name`           | `str`    | 相机名称。                                    |
| `animated`       | `bool`   | 启用/禁用相机动画。                            |
| `aspectRatio`    | `float`  | 图像宽高比。                                   |
| `focalLength`    | `float`  | 焦距（毫米）。                                 |
| `frameHeight`    | `float`  | 帧高度（毫米）。                               |
| `focalDistance`   | `float`  | 对焦距离（场景单位）。                          |
| `apertureRadius` | `float`  | 光圈半径（场景单位）。                          |
| `shutterSpeed`   | `float`  | 快门速度（秒，未实现）。                        |
| `ISOSpeed`       | `float`  | 感光度（未实现）。                              |
| `nearPlane`      | `float`  | 近裁剪面距离（场景单位）。                      |
| `farPlane`       | `float`  | 远裁剪面距离（场景单位）。                      |
| `position`       | `float3` | 相机在世界空间中的位置。                        |
| `target`         | `float3` | 相机在世界空间中的目标点。                      |
| `up`             | `float3` | 相机在世界空间中的上向量。                      |

#### EnvMap

class falcor.**EnvMap**

| Property    | Type     | Description                                     |
|-------------|----------|-------------------------------------------------|
| `path`      | `str`    | 已加载的环境贴图文件路径（只读）。                |
| `rotation`  | `float3` | 旋转角度（XYZ，单位：度）。                       |
| `intensity` | `float`  | 强度（标量乘数）。                                |

| Static method          | Description                           |
|------------------------|---------------------------------------|
| `createFromFile(path)` | 从文件创建环境贴图。                    |

#### Material

**已弃用**：请改用 `StandardMaterial`。

#### StandardMaterial

enum falcor.**MaterialTextureSlot**

`BaseColor`, `Specular`, `Emissive`, `Normal`, `Transmission`, `Displacement`

enum falcor.**AlphaMode**

`Opaque`, `Mask`

enum falcor.**ShadingModel**

`MetalRough`, `SpecGloss`

class falcor.**StandardMaterial**

| Property               | Type           | Description                                          |
|------------------------|----------------|------------------------------------------------------|
| `name`                 | `str`          | 材质名称。                                            |
| `shadingModel`         | `ShadingModel` | 着色模型（只读）。                                     |
| `baseColor`            | `float4`       | 基础颜色（线性 RGB）和不透明度（alpha）。               |
| `specularParams`       | `float4`       | 高光参数。                                            |
| `roughness`            | `float`        | 粗糙度（0 = 光滑，1 = 粗糙）。                        |
| `metallic`             | `float`        | 金属度（0 = 电介质，1 = 导体）。                       |
| `transmissionColor`    | `float3`       | 透射颜色。                                            |
| `diffuseTransmission`  | `float`        | 漫反射透射（0 = 不透明，1 = 透明）。                   |
| `specularTransmission` | `float`        | 高光透射（0 = 不透明，1 = 透明）。                     |
| `indexOfRefraction`    | `float`        | 折射率。                                              |
| `emissiveColor`        | `float3`       | 自发光颜色（线性 RGB）。                               |
| `emissiveFactor`       | `float`        | 自发光颜色乘数。                                      |
| `alphaMode`            | `AlphaMode`    | Alpha 模式（不透明或遮罩）。                           |
| `alphaThreshold`       | `float`        | Alpha 遮罩阈值（0-1）。                               |
| `doubleSided`          | `bool`         | 启用双面渲染。                                        |
| `thinSurface`          | `bool`         | 启用薄表面渲染。                                      |
| `nestedPriority`       | `int`          | 嵌套电介质的嵌套优先级。                               |
| `volumeAbsorption`     | `float3`       | 体积吸收系数。                                        |
| `volumeScattering`     | `float3`       | 体积散射系数。                                        |
| `volumeAnisotropy`     | `float`        | 体积相函数各向异性（g）。                              |
| `displacementScale`    | `float`        | 位移贴图缩放值。                                      |
| `displacementOffset`   | `float`        | 位移贴图偏移值。                                      |

| Method                                  | Description                                |
|-----------------------------------------|--------------------------------------------|
| `clearTexture(slot)`                    | 清除纹理槽之一。                            |
| `loadTexture(slot, path, useSrgb=True)` | 从文件加载纹理槽之一。                      |

#### HairMaterial

class falcor.**HairMaterial**

接口与 `StandardMaterial` 相同。

#### ClothMaterial

class falcor.**ClothMaterial**

接口与 `StandardMaterial` 相同。

#### Grid

class falcor.**Grid**

| Property     | Type    | Description                                           |
|--------------|---------|-------------------------------------------------------|
| `voxelCount` | `int`   | 网格中活动体素的总数（只读）。                          |
| `minIndex`   | `int3`  | 网格中存储的最小索引（只读）。                          |
| `maxIndex`   | `int3`  | 网格中存储的最大索引（只读）。                          |
| `minValue`   | `float` | 网格中存储的最小值（只读）。                            |
| `maxValue`   | `float` | 网格中存储的最大值（只读）。                            |

| Method          | Description                                            |
|-----------------|--------------------------------------------------------|
| `getValue(ijk)` | 访问网格中体素的值（索引空间）。                         |

| Static method                                                | Description                                 |
|--------------------------------------------------------------|---------------------------------------------|
| `createSphere(radius, voxelSize, blendRange=2.0)`            | 创建球体网格。                               |
| `createBox(width, height, depth, voxelSize, blendRange=2.0)` | 创建盒体网格。                               |
| `createFromFile(path, gridname)`                             | 从 OpenVDB/NanoVDB 文件创建网格。            |

#### Volume

**已弃用**：请改用 `GridVolume`。

#### GridVolume

enum falcor.GridVolume.**GridSlot**

`Density`, `Emission`

enum falcor.GridVolume.**EmissionMode**

`Direct`, `Blackbody`

class falcor.**GridVolume**

| Property              | Type           | Description                                             |
|-----------------------|----------------|---------------------------------------------------------|
| `name`                | `str`          | 体积名称。                                               |
| `gridFrame`           | `int`          | 网格序列中的当前帧。                                      |
| `gridFrameCount`      | `int`          | 网格序列中的总帧数（只读）。                               |
| `frameRate`           | `float`        | 网格动画帧率。                                            |
| `playbackEnabled`     | `bool`         | 启用/禁用网格动画播放。                                    |
| `densityGrid`         | `Grid`         | 密度网格。                                                |
| `densityScale`        | `float`        | 密度缩放因子。                                            |
| `emissionGrid`        | `Grid`         | 自发光网格。                                              |
| `emissionScale`       | `float`        | 自发光缩放因子。                                          |
| `albedo`              | `float3`       | 散射反照率。                                              |
| `anisotropy`          | `float`        | 相函数各向异性（g）。                                      |
| `emissionMode`        | `EmissionMode` | 自发光模式（Direct, Blackbody）。                          |
| `emissionTemperature` | `float`        | 自发光基础温度（K）。                                      |

| Method                                    | Description                                                                         |
|-------------------------------------------|-------------------------------------------------------------------------------------|
| `loadGrid(slot, path, gridname)`          | 从 OpenVDB/NanoVDB 文件加载网格槽。                                                  |
| `loadGridSequence(slot, paths, gridname)` | 从一系列 OpenVDB/NanoVDB 文件加载网格槽。                                             |
| `loadGridSequence(slot, path, gridname)`  | 从目录中包含的一系列 OpenVDB/NanoVDB 文件加载网格槽。                                  |

#### Light

class falcor.**Light**

| Property    | Type     | Description                     |
|-------------|----------|---------------------------------|
| `name`      | `str`    | 光源名称。                       |
| `active`    | `bool`   | 启用/禁用光源。                  |
| `animated`  | `bool`   | 启用/禁用光源动画。              |
| `intensity` | `float3` | 光源强度。                       |

class falcor.**PointLight** : falcor.**Light**

| Property        | Type     | Description                            |
|-----------------|----------|----------------------------------------|
| `position`      | `float3` | 光源在世界空间中的位置。                |
| `direction`     | `float3` | 光源在世界空间中的方向。                |
| `openingAngle`  | `float`  | 张角半角（弧度）。                      |
| `penumbraAngle` | `float`  | 半影半角（弧度）。                      |

class falcor.**DirectionalLight** : falcor.**Light**

| Property    | Type     | Description                            |
|-------------|----------|----------------------------------------|
| `direction` | `float3` | 光源在世界空间中的方向。                |

class falcor.**DistantLight** : falcor.**Light**

| Property    | Type     | Description                                   |
|-------------|----------|-----------------------------------------------|
| `direction` | `float3` | 光源在世界空间中的方向。                        |
| `angle`     | `float`  | 光源对张半角（弧度）。                          |

class falcor.**AnalyticAreaLight** : falcor.**Light**

class falcor.**RectLight** : falcor.**AnalyticAreaLight**

class falcor.**DiscLight** : falcor.**AnalyticAreaLight**

class falcor.**SphereLight** : falcor.**AnalyticAreaLight**

#### Transform

class falcor.**Transform**

| Property           | Type       | Description                                 |
|--------------------|------------|---------------------------------------------|
| `translation`      | `float3`   | 平移。                                       |
| `rotationEuler`    | `float3`   | 绕 XYZ 轴的欧拉旋转角（弧度）。              |
| `rotationEulerDeg` | `float3`   | 绕 XYZ 轴的欧拉旋转角（度）。                |
| `scaling`          | `float3`   | 缩放。                                       |
| `matrix`           | `float4x4` | 变换矩阵（只读）。                           |

| Method                         | Description                      |
|--------------------------------|----------------------------------|
| `lookAt(position, target, up)` | 设置观察变换。                    |

#### Animation

enum falcor.Animation.**InterpolationMode**

`Linear`, `Hermite`

enum falcor.Animation.**Behavior**

`Constant`, `Linear`, `Cycle`, `Oscillate`

class falcor.**Animation**

| Property               | Type                | Description                                                              |
|------------------------|---------------------|--------------------------------------------------------------------------|
| `name`                 | `str`               | 动画名称（只读）。                                                        |
| `nodeID`               | `int`               | 动画的场景图节点（只读）。                                                |
| `duration`             | `float`             | 持续时间（秒，只读）。                                                    |
| `interpolationMode`    | `InterpolationMode` | 插值模式（linear, hermite）。                                             |
| `preInfinityBehavior`  | `Behavior`          | 第一个关键帧之前的行为（constant, linear, cycle, oscillate）。             |
| `postInfinityBehavior` | `Behavior`          | 最后一个关键帧之后的行为（constant, linear, cycle, oscillate）。           |
| `enableWarping`        | `bool`              | 启用/禁用扭曲，即从最后一个关键帧到第一个关键帧的插值。                    |

| Method                         | Description                                  |
|--------------------------------|----------------------------------------------|
| `addKeyframe(time, transform)` | 在给定时间添加变换关键帧。                     |

#### TriangleMesh

class falcor.TriangleMesh.**Vertex**

| Field      | Type     | Description                |
|------------|----------|----------------------------|
| `position` | `float3` | 顶点位置。                  |
| `normal`   | `float3` | 顶点法线。                  |
| `texCoord` | `float2` | 顶点纹理坐标。              |

class falcor.**TriangleMesh**

| Property   | Type           | Description                  |
|------------|----------------|------------------------------|
| `name`     | `str`          | 三角网格名称。                |
| `vertices` | `list(Vertex)` | 顶点列表（只读）。           |
| `indices`  | `list(int)`    | 索引列表（只读）。           |

| Method                                  | Description                                         |
|-----------------------------------------|-----------------------------------------------------|
| `addVertex(position, normal, texCoord)` | 向网格添加顶点。返回顶点索引。                       |
| `addTriangle(i0, i1, i2)`               | 向网格添加三角形。                                   |

| Class Method                                         | Description                                                                                                                                       |
|------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| `createQuad(size=float2(1))`                         | 创建四边形网格，居中于原点，法线指向 Y 轴正方向。                                                                                                  |
| `createCube(size=float3(1))`                         | 创建立方体网格，居中于原点。                                                                                                                      |
| `createSphere(radius=1, segmentsU=32, segmentsV=16)` | 创建 UV 球体网格，居中于原点，极点位于 Y 轴正/负方向。                                                                                             |
| `createFromFile(path, smoothNormals=False)`          | 从文件创建三角网格。如果文件中未定义法线，可使用 `smoothNormals` 生成平滑法线而非面法线。                                                            |

#### SceneBuiler

enum falcor.**SceneBuilderFlags**

| Enum                         | Description                                                                                                                                                                                           |
|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Default`                    | 使用默认标志 (0)。                                                                                                                                                                                     |
| `DontMergeMaterials`         | 不合并具有相同属性的材质。使用此选项可保留原始材质名称。                                                                                                                                                |
| `UseOriginalTangentSpace`    | 使用随网格加载的原始副切线。默认情况下会忽略它们，使用 MikkTSpace 生成切线空间。如果缺少副切线，将始终生成。                                                                                             |
| `AssumeLinearSpaceTextures`  | 默认情况下，表示颜色的纹理（漫反射/高光）被解释为 sRGB 数据。使用此标志强制颜色纹理为线性空间。                                                                                                         |
| `DontMergeMeshes`            | 保留场景中的原始网格列表，不合并具有相同材质的网格。                                                                                                                                                    |
| `UseSpecGlossMaterials`      | 设置材质使用 Spec-Gloss 着色模型。否则，OBJ 默认使用 Spec-Gloss，其他格式默认使用 Metal-Rough。                                                                                                        |
| `UseMetalRoughMaterials`     | 设置材质使用 Metal-Rough 着色模型。否则，OBJ 默认使用 Spec-Gloss，其他格式默认使用 Metal-Rough。                                                                                                       |
| `NonIndexedVertices`         | 将网格转换为非索引顶点。这需要更多内存，但可能提高性能。                                                                                                                                                |
| `Force32BitIndices`          | 强制所有网格使用 32 位索引。默认情况下，小型网格使用 16 位索引。                                                                                                                                        |
| `RTDontMergeStatic`          | 对于光线追踪，不将所有静态非实例化网格合并到单个预变换 BLAS 中。                                                                                                                                        |
| `RTDontMergeDynamic`         | 对于光线追踪，不将具有相同变换的动态非实例化网格合并到单个 BLAS 中。                                                                                                                                    |
| `RTDontMergeInstanced`       | 对于光线追踪，不将具有相同实例的实例化网格合并到单个 BLAS 中。                                                                                                                                          |
| `FlattenStaticMeshInstances` | 通过复制网格数据和组合变换来展平静态网格实例。动画实例不受影响。可能导致内存使用大幅增加。                                                                                                                |
| `DontOptimizeGraph`          | 不优化场景图以移除不必要的节点。                                                                                                                                                                       |
| `DontOptimizeMaterials`      | 不通过移除常量纹理来优化材质。优化是无损的，通常应启用。                                                                                                                                                |
| `DontUseDisplacement`        | 不使用位移贴图。                                                                                                                                                                                       |
| `UseCache`                   | 启用场景缓存。将运行时场景表示缓存到磁盘以减少加载时间。                                                                                                                                                |
| `RebuildCache`               | 重建场景缓存。                                                                                                                                                                                        |

class falcor.**SceneBuilder**

| Property         | Type                  | Description                                      |
|------------------|-----------------------|--------------------------------------------------|
| `flags`          | `SceneBuilderFlags`   | 场景构建器标志（只读）。                           |
| `renderSettings` | `SceneRenderSettings` | 决定场景渲染方式的设置。                           |
| `materials`      | `list(Material)`      | 材质列表（只读）。                                 |
| `volumes`        | `list(Volume)`        | **已弃用**：请改用 `gridVolumes`。                 |
| `gridVolumes`    | `list(GridVolume)`    | 网格体积列表（只读）。                             |
| `lights`         | `list(Light)`         | 光源列表（只读）。                                 |
| `cameras`        | `list(Camera)`        | 相机列表（只读）。                                 |
| `animations`     | `list(Animation)`     | 动画列表（只读）。                                 |
| `envMap`         | `EnvMap`              | 环境贴图。                                        |
| `selectedCamera` | `Camera`              | 默认选中的相机。                                   |
| `cameraSpeed`    | `float`               | 交互式相机的速度。                                 |

| Method                                        | Description                                                                                                     |
|-----------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| `importScene(path, dict, instances)`          | 从资产文件加载场景。`dict` 包含可选数据。`instances` 是可选的 `Transform` 列表。                                   |
| `addTriangleMesh(triangleMesh, material)`     | 向场景添加三角网格并返回其 ID。                                                                                  |
| `addMaterial(material)`                       | 添加材质并返回其 ID。                                                                                           |
| `getMaterial(name)`                           | 按名称返回材质。返回第一个匹配的材质，如果未找到则返回 `None`。                                                   |
| `loadMaterialTexture(material, slot, path)`   | 请求异步加载材质纹理。使用 `Material.loadTexture` 进行同步加载。                                                  |
| `waitForMaterialTextureLoading()`             | 等待所有材质纹理加载完成。                                                                                       |
| `addVolume(volume)`                           | **已弃用**：请改用 `addGridVolume`。                                                                             |
| `addGridVolume(gridVolume)`                   | 添加网格体积并返回其 ID。                                                                                        |
| `getVolume(name)`                             | **已弃用**：请改用 `getGridVolume`。                                                                             |
| `getGridVolume(name)`                         | 按名称返回网格体积。返回第一个匹配的体积，如果未找到则返回 `None`。                                                |
| `addLight(light)`                             | 添加光源并返回其 ID。                                                                                            |
| `getLight(name)`                              | 按名称返回光源。返回第一个匹配的光源，如果未找到则返回 `None`。                                                    |
| `addCamera(camera)`                           | 添加相机并返回其 ID。                                                                                            |
| `addAnimation(animation)`                     | 添加动画。                                                                                                       |
| `createAnimation(animatable, name, duration)` | 为可动画对象创建动画。返回新动画，如果已存在则返回 `None`。                                                        |
| `addNode(name, transform, parent)`            | 添加节点并返回其 ID。                                                                                            |
| `addMeshInstance(nodeID, meshID)`             | 添加网格实例。                                                                                                   |
| `addCustomPrimitive(userID, aabb)`            | 添加自定义图元。`aabb` 是指定其边界的 AABB。                                                                      |
| `addSDFGridInstance(userID, sdfGridID)`       | 添加 SDF 网格实例。                                                                                              |
| `addSDFGrid(sdfGrid, maternal)`               | 添加 SDF 网格并返回其 ID。                                                                                       |


### Render Pass 辅助工具

#### IOSize

enum falcor.RenderPassHelpers.**IOSize**

| Enum              | Description                                                                                                               |
|-------------------|---------------------------------------------------------------------------------------------------------------------------|
| Default           | 使用默认大小。大小根据绑定的内容确定（系统默认使用窗口大小）。                                                               |
| Fixed             | 使用固定像素大小。                                                                                                         |
| Full              | 使用完整窗口大小。                                                                                                         |
| Half              | 使用一半窗口大小。                                                                                                         |
| Quarter           | 使用四分之一窗口大小。                                                                                                     |
| Double            | 使用两倍窗口大小。                                                                                                         |

部分 render pass 提供使用 `IOSize` 枚举设置输出分辨率的选项。
使用示例（在创建 render pass 时将这些选项添加到脚本字典中）：

```python
'outputSize': IOSize.Half
```

```python
'outputSize': IOSize.Fixed, 'fixedOutputSize': uint2(128,64)
```

### Render Passes

**注意：** 这些文档不完整。请参阅源代码以获取导出方法和属性的完整集合。

#### AccumulatePass

enum falcor.**AccumulatePrecision**

`Double`, `Single`, `SingleCompensated`

class falcor.**AccumulatePass**

| Method    | Description                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `reset()` | 重置累积。当 pass 以 `'autoReset': False` 创建时非常有用。                                  |

| Property                | Type        | Description                                                                   |
|-------------------------|-------------|-------------------------------------------------------------------------------|
| `outputSize`            | `IOSize`    | 设置输出分辨率。                                                               |
| `fixedOutputSize`       | `uint2`     | 使用 `IOSize.Fixed` 时的固定输出分辨率（宽度，高度），单位为像素。               |

#### ToneMapper

enum falcor.**ToneMapOp**

`Linear`, `Reinhard`, `ReinhardModified`, `HejiHableAlu`, `HableUc2`, `Aces`

class falcor.**ToneMapper**

| Property                | Type        | Description                                                                   |
|-------------------------|-------------|-------------------------------------------------------------------------------|
| `exposureCompensation`  | `float`     | 曝光补偿（适用于手动和自动曝光）。                                              |
| `autoExposure`          | `bool`      | 启用/禁用自动曝光。                                                            |
| `exposureValue`         | `float`     | 手动模式下的曝光值。                                                            |
| `filmSpeed`             | `float`     | 手动模式下的 ISO 感光度。                                                       |
| `whiteBalance`          | `bool`      | 启用/禁用白平衡。                                                              |
| `whitePoint`            | `float`     | 白点色温（开尔文）。                                                            |
| `operator`              | `ToneMapOp` | 色调映射运算符。                                                                |
| `clamp`                 | `bool`      | 启用/禁用钳制到 [0..1] 范围。                                                   |
| `outputSize`            | `IOSize`    | 设置输出分辨率。                                                                |
| `fixedOutputSize`       | `uint2`     | 使用 `IOSize.Fixed` 时的固定输出分辨率（宽度，高度），单位为像素。                |

#### SimplePostFX

class falcor.**SimplePostFX**

| Property                | Type        | Description                                                                   |
|-------------------------|-------------|-------------------------------------------------------------------------------|
| `outputSize`            | `IOSize`    | 设置输出分辨率。                                                               |
| `fixedOutputSize`       | `uint2`     | 使用 `IOSize.Fixed` 时的固定输出分辨率（宽度，高度），单位为像素。               |

#### GBuffer passes

class falcor.**GBufferRaster**
class falcor.**VBufferRaster**
class falcor.**GBufferRT**
class falcor.**VBufferRT**

| Property                | Type        | Description                                                                   |
|-------------------------|-------------|-------------------------------------------------------------------------------|
| `outputSize`            | `IOSize`    | 设置输出分辨率。                                                               |
| `fixedOutputSize`       | `uint2`     | 使用 `IOSize.Fixed` 时的固定输出分辨率（宽度，高度），单位为像素。               |

#### ImageLoader

class falcor.**ImageLoader**

| Property                | Type        | Description                                                                   |
|-------------------------|-------------|-------------------------------------------------------------------------------|
| `outputSize`            | `IOSize`    | 设置输出分辨率。                                                               |

当使用 `IOSize.Fixed` 时，render pass 输出为已加载图像的原始分辨率。在所有其他模式下，图像将双线性缩放到所需的输出分辨率。

#### GaussianBlur

class falcor.**GaussianBlur**

| Property      | Type    | Description             |
|---------------|---------|-------------------------|
| `kernelWidth` | `int`   | 核宽度（像素）。         |
| `sigma`       | `float` | 高斯 sigma 值。         |

#### SSAO

class falcor.**SSAO**

| Property       | Type    | Description              |
|----------------|---------|--------------------------|
| `kernelRadius` | `int`   | 核半径（像素）。          |
| `sampleRadius` | `float` | 采样半径。                |
| `distribution` | `int`   | 采样分布。                |

#### Skybox

class falcor.**SkyBox**

| Property | Type    | Description       |
|----------|---------|-------------------|
| `scale`  | `float` | 颜色乘数。         |
| `filter` | `int`   | 过滤模式。         |

#### CSM

class falcor.**CSM**

| Property             | Type    | Description |
|----------------------|---------|-------------|
| `cascadeCount`       | `int`   |             |
| `mapSize`            | `uint2` |             |
| `visibilityBitCount` | `int`   |             |
| `filter`             | `int`   |             |
| `sdsmLatency`        | `int`   |             |
| `partition`          | `int`   |             |
| `lambda`             | `float` |             |
| `minDistance`        | `float` |             |
| `maxDistance`        | `float` |             |
| `cascadeThreshold`   | `float` |             |
| `depthBias`          | `float` |             |
| `kernelWidth`        | `int`   |             |
| `maxAniso`           | `int`   |             |
| `bleedReduction`     | `float` |             |
| `positiveExp`        | `float` |             |
| `negativeExp`        | `float` |             |

#### FXAA

class falcor.**FXAA**

| Property           | Type    | Description |
|--------------------|---------|-------------|
| `qualitySubPix`    | `float` |             |
| `edgeThreshold`    | `float` |             |
| `edgeThresholdMin` | `float` |             |
| `earlyOut`         | `bool`  |             |

#### TAA

class falcor.**TAA**

| Property | Type    | Description |
|----------|---------|-------------|
| `alpha`  | `float` |             |
| `sigma`  | `float` |             |
