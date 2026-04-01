### [Index](../index.md) | [Usage](./index.md) | SDF 编辑器

--------

# SDF 编辑器

## 快速入门
1. 构建 Falcor。
2. 启动 Mogwai。
3. 加载脚本 `data/sdf-editor/SDFEditor.py`。该脚本使用了 `PathTracer`、`AccumulatePass`、`ToneMapper`，并在末尾包含一个 `SDFEditor` pass。
4. 加载场景 `data/sdf-editor/SDFEditorStartScene.pyscene`。您应该会看到窗口中心有一个球体。现在您可以开始使用 SDF 编辑器了。下拉 `SDFEditor` `RenderPass` 的 GUI。将鼠标悬停在工具提示（即顶部的"(?)"）上，以获取使用编辑器的说明。
    - `ALT` 允许您在按住鼠标左键的同时添加/减去几何体。
    - `TAB` 打开用于选择图元和图元操作的 GUI。

### 文件格式
Falcor 目前支持两种 SDF 文件格式：
- `.sdf`：以文本文件形式存储"编辑"列表，以及
    - 注意 `SDFEditorStartScene.pyscene`（见快速入门）加载的是 `single_sphere.sdf`，其中只包含一个球体。
    - 您可以改为加载 `test_primitives.sdf` 以查看其他图元。
- `.sdfg`：以二进制文件形式存储有符号距离场。

但是，SDF 编辑器仅支持加载 `.sdf` 格式，但可以保存为 `.sdfg` 文件（这可能会在将来更改）。

## SDF 编辑器 RenderPass

SDF 编辑器实现为一个 render pass，其输入和输出如下：
| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `vbuffer` | 输入 | 编码主要命中点。 |
| `linearZ` | 输入 | 线性深度。 |
| `inputColor` | 输入 | 来自上一个 render pass 的颜色纹理。 |
| `output` | 输出 | 最终颜色纹理。 |

使用路径追踪器的简单渲染脚本可以在 `data/sdf-editor/SDFEditor.py` 中查看。

要开始使用 SDF 编辑器进行编辑，场景中需要至少有一个 SBS 类型的 SDF 网格实例。SDF 网格通过调用 `SDFGrid.createSBS()` 创建。它接受两个可选参数。一个是砖块宽度（`brickWidth`），即每个 AABB 中应打包的体素数量以进行光线追踪，另一个参数是是否压缩数据（`compressed`）。创建函数返回一个 SBS 网格对象，可以通过从 SceneBuilder 调用 `addSDFGrid()` 将其添加到场景中。

如果您不想从头开始，可以使用 SDF 网格对象的 `loadPrimitivesFromFile` 函数从内存加载 SDF 文件。该函数接收文件路径（`path`）和网格宽度（`gridWidth`）。网格宽度是网格的期望分辨率（如果与砖块宽度不对齐，内部实现可能会增加网格宽度）。

## 在 pyscene 文件中创建数据结构
典型的设置如下：
```
sdfGrid = SDFGrid.createSVS()
sdfGrid.loadValuesFromFile(path="test_primitives.sdf")
sceneBuilder.addSDFGridInstance(
    sceneBuilder.addNode('SDFGrid', Transform(translation=float3(0, 0.6, 0))),
    sceneBuilder.addSDFGrid(sdfGrid, sdfGridMaterial)
)
```
支持的网格数据结构包括：
- 归一化密集网格（NDG，即完整的 mip 层次结构）：`sdfGrid = SDFGrid.createNDGrid(narrowBandThickness=2.5)`（在 SM 上进行遍历）
- 稀疏体素八叉树（SVO）：`sdfGrid = SDFGrid.createSVO()`（在 SM 上进行遍历）
- 稀疏体素集（SVS）：`sdfGrid = SDFGrid.createSVS()`（使用 TTU，通常是最快的方法，但使用大量内存）
- 稀疏砖块集（SBS）：`sdfGrid = SDFGrid.createSBS()`（这是性能和内存使用之间的**最佳**折衷方案。结合了 TTU 与 SM 上的遍历。）
