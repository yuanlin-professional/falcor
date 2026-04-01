### [Index](./index.md) | 快速入门

--------

# 快速入门

## 项目结构

### Falcor
`Source/Falcor` 文件夹包含 Falcor 核心框架，它以共享库的形式构建。

### Samples
`Source/Samples` 文件夹包含 Falcor 示例应用程序。每个应用程序直接使用 Falcor，演示了如何使用 Falcor 提供的一些基本功能和抽象。

### Mogwai
`Source/Mogwai` 文件夹包含 Mogwai 应用程序。它是使用渲染图的主要应用程序，并提供了一些实用工具。一些示例渲染图位于其项目文件夹下：`Source/Mogwai/Data/`。

### RenderPasses
`Source/RenderPasses` 文件夹包含多个组件（共享库），它们是创建渲染图的构建模块。所有渲染通道库都会作为 `Mogwai` 应用程序的依赖项自动构建。

-----------------------
## 工作流程
使用 Falcor 时有两种主要工作流程：

### 渲染图
在原型设计或实现渲染技术时，推荐的工作流程是创建渲染通道、渲染图，然后使用 Mogwai 进行渲染。[教程](./tutorials/index.md)重点介绍了这一工作流程。

#### 运行示例渲染图：
1. 构建 Falcor
2. 运行 `Mogwai`
3. 按 `Ctrl+O`，或从顶部菜单栏选择 `File` -> `Load Script`
4. 在 `Source/Mogwai/Data/` 中选择一个渲染图（.py 文件），例如 `ForwardRenderer.py`。
5. 按 `Ctrl+Shift+O`，或从顶部菜单栏选择 `File` -> `Load Scene`。
6. 选择一个场景或模型，例如 `media/Arcade/Arcade.pyscene`

场景和渲染图也可以通过拖放方式加载。

#### 创建渲染通道库：
运行 `tools/make_new_render_pass.bat <Name>` 来创建一个新的渲染通道库。

### 示例应用程序
在某些情况下，用户可能仍然希望直接使用 Falcor 创建应用程序。`Renderer` 类是 Falcor 应用程序的引导类。您应该继承该类并重写其受保护的方法，这些方法用作回调函数。`ModelViewer` 示例是一个很好的参考起点。

#### 创建新的示例应用程序：
运行 `tools/make_new_sample_app.bat <Name>` 来创建一个新的示例应用程序。

-----------------------

## 使用 Shader 和数据文件
Falcor 会在多个工作目录中搜索以相对路径指定的文件。

*数据*文件是指非 shader 资源，例如纹理和模型。

从 Visual Studio 运行时：
- Falcor 在以下位置查找数据文件：
    - 项目文件夹（包含 Visual Studio 项目文件的目录）内名为 `Data` 的子文件夹。
    - 可执行文件目录内名为 `Data` 的子文件夹。
    - 一个可选的名为 `FALCOR_MEDIA_FOLDERS` 的环境变量，包含以分号分隔的文件夹列表。
    - 通过调用 `addDataDirectory()` 添加到数据目录列表中的任何目录。
- Falcor 在项目文件夹的相对路径下查找 Shader 文件。

构建时，项目的 `Data` 文件夹和 shader 文件将自动部署到可执行文件目录中的 `Data` 和 `Shaders` 文件夹，同时保留文件夹层次结构。从可执行文件运行应用程序时，Falcor 将在这些文件夹中搜索。这使得构建输出文件夹具有自包含性，便于共享。

最佳实践是在**项目**文件旁创建一个名为 `Data` 的目录，并将所有数据文件放在那里。您的 shader 文件应使用 `.slang`、`.slangh`、`.hlsl` 或 `.hlsli` 扩展名。扩展名为 `.h` 的头文件应仅用于宿主端文件。需要在宿主端和 shader 之间共享的头文件应使用 `.slang` 或 `.slangh` 扩展名。

要搜索数据或 shader 文件，请分别调用 `findFileInDataDirectories()` 或 `findFileInShaderDirectories()`。

Falcor 使用 [Slang](https://github.com/shader-slang/slang) 着色语言和编译器。
用户可以在 `.hlsl` 或 `.slang` 文件中编写 HLSL/Slang shader 代码。
