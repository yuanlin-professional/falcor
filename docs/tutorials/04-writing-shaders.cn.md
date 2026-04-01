### [Index](../index.md) | [教程](./index.md) | 编写 Shader

--------

# 编写 Shader

现在我们已经编写了基本的 render pass 和 render graph，让我们来看看如何编写使用 shader 的更复杂的 pass。Falcor 使用 Slang 着色语言和编译器，文件应使用以下扩展名之一：`.slang`、`.slangh`、`.hlsl`、`.hlsli`。有关在 Falcor 中使用 shader 和数据文件的最佳实践，请参阅 [Getting Started](../getting-started.md) 页面中的 *Using Shaders and Data Files* 部分。

在本教程中，我们将创建一个将场景渲染为特定颜色线框的 pass。

## 编写简单的 Shader
本示例中的 shader 是一个简单的单行像素 shader，我们可以向其传递一个颜色值，它将返回该颜色作为每个像素的颜色。为此，我们需要一个包含颜色输入的常量缓冲区以及一个返回此输入的函数。

```c++
import Scene.Raster; // Imports defaultVS.

cbuffer PerFrameCB
{
    float4 gColor;
};

VSOut vsMain(VSIn vIn)
{
    return defaultVS(vIn);
}

float4 psMain() : SV_TARGET
{
    return gColor;
}
```

注意，我们在这里没有指定 register 或 space。这不是必需的，因为 Slang 会自动分配 shader 资源的绑定位置，Falcor 允许你通过名称引用这些资源。但如果需要，仍然支持手动分配。

## 创建 `WireframePass`
按照教程 3 中描述的方法设置 pass，并将 shader 文件添加到项目中。`reflect()` 需要返回包含单个输出字段的 `RenderPassReflection`，并将所有描述更改为 "Renders a scene as a wireframe"。我们还需要向类添加以下私有变量：

```c++
Scene::SharedPtr mpScene;
GraphicsProgram::SharedPtr mpProgram;
GraphicsState::SharedPtr mpGraphicsState;
RasterizerState::SharedPtr mpRasterState;
GraphicsVars::SharedPtr mpVars;
```

让我们看看其他函数，其中大部分将比 `ExampleBlitPass` 中的更复杂。

### `WireframePass()`
虽然 `create()` 只需调用构造函数并返回包装在 `SharedPtr` 中的结果，但我们需要在构造函数中初始化这些对象：
- `GraphicsProgram::SharedPtr mpProgram` - 通过调用 `GraphicsProgram::createFromFile()` 并传入文件名以及 shader 代码中顶点和像素 shader 函数的名称来初始化。
- `RasterizerState::SharedPtr mpRasterState` - 在我们的示例中，我们希望 `FillMode` 为 `Wireframe`，`CullMode` 为 `None`。为此，先创建一个 `RasterizerState::Desc`，然后使用 `setFillMode()` 和 `setCullMode()` 设置这些值。接着，通过调用 `RasterizerState::create()` 并传入 `Desc` 对象作为参数来创建 `RasterizerState` 对象。
- `GraphicsState::SharedPtr mpGraphicsState` - 我们可以通过调用 `GraphicsState::create()` 创建新的 `GraphicsState`，并通过调用 `setProgram()` 和 `setRasterizerState()` 来绑定 `mpProgram` 和 `mpRasterState`。

构造函数应类似于以下代码：
```c++
WireframePass::WireframePass()
{
    mpProgram = GraphicsProgram::createFromFile("RenderPasses/Wireframe/Wireframe.3d.slang", "vsMain", "psMain");
    RasterizerState::Desc wireframeDesc;
    wireframeDesc.setFillMode(RasterizerState::FillMode::Wireframe);
    wireframeDesc.setCullMode(RasterizerState::CullMode::None);
    mpRasterState = RasterizerState::create(wireframeDesc);

    mpGraphicsState = GraphicsState::create();
    mpGraphicsState->setProgram(mpProgram);
    mpGraphicsState->setRasterizerState(mpRasterState);
}
```

### `reflect()`
如同 _实现 Render Pass_ 教程中所述，你只需定义线框视图的输出。
```
RenderPassReflection WireframePass::reflect(const CompileData& compileData)
{
    RenderPassReflection reflector;
    reflector.addOutput("output", "Wireframe view texture");
    return reflector;
}
```

### `setScene()`
我们的第一个 render pass 不需要 `Scene` 对象；但这个 pass 需要，并且需要此函数来设置 `mpScene`。我们首先需要将 `mpScene` 设置为传入的场景，然后将所有场景定义添加到 `mpProgram` 中。接着我们创建 `GraphicsVars`，以便稍后在 `execute()` 中绑定 shader 变量。实现如下：
```c++
void WireframePass::setScene(RenderContext* pRenderContext, const Scene::SharedPtr& pScene)
{
    mpScene = pScene;
    if (mpScene) mpProgram->addDefines(mpScene->getSceneDefines());
    mpVars = GraphicsVars::create(mpProgram->getReflector());
}
```
#### 为什么需要场景定义？
我们需要某种方式告诉 Slang/HLSL 场景需要绑定多少资源，并确保场景数据可以在 shader 之间移植。然而，目前没有自动完成此操作的方法，因此我们使用场景定义来传达这些信息。

### `execute()`
此函数需要执行多个操作：为输出创建并绑定 FBO 到 `GraphicsState`，设置渲染状态，以及通过调用 `Scene::render()` 渲染场景。

#### 创建和绑定 FBO
我们可以通过先对输出纹理调用 `Fbo::create()` 来创建和绑定 FBO，清除它以移除之前执行的数据（防止移动摄像机时留下永久痕迹），然后调用 `GraphicsState::setFbo()` 来绑定它。这一步如下所示：
```c++
auto pTargetFbo = Fbo::create({ renderData.getTexture("output") });
const float4 clearColor(0, 0, 0, 1);
pRenderContext->clearFbo(pTargetFbo.get(), clearColor, 1.0f, 0, FboAttachmentType::All);
mpGraphicsState->setFbo(pTargetFbo);
```

#### 绑定 Shader
绑定 shader 值也相当简单，因为 Falcor 允许你像设置字典中的值一样在 `GraphicsVars` 对象中设置 shader 值。我们的 shader 需要一个颜色值 `gColor`，它位于 `perFrameCB` 常量缓冲区内。这一步应如下所示：
```c++
mpVars["perFrameCB"]["gColor"] = float4(0, 1, 0, 1);
```

#### 使用 Shader 渲染场景
在场景、shader 以及 `GraphicsState` 和 `RasterizerState` 都设置好之后，我们终于可以在 `execute()` 的末尾渲染场景了。这是通过 `mpScene` 的 `render()` 方法完成的，如下所示：
```c++
mpScene->rasterize(pRenderContext, mpGraphicsState.get(), mpVars.get(), mpRasterState, mpRasterState);
```
你的 `execute()` 函数现在应该如下所示，其中包含对 `mpScene` 的检查以避免在场景未设置时访问它：
```c++
void WireframePass::execute(RenderContext* pRenderContext, const RenderData& renderData)
{
    auto pTargetFbo = Fbo::create({renderData.getTexture("output")});
    const float4 clearColor(0, 0, 0, 1);
    pRenderContext->clearFbo(pTargetFbo.get(), clearColor, 1.0f, 0, FboAttachmentType::All);
    mpGraphicsState->setFbo(pTargetFbo);

    if (mpScene)
    {
        mpVars["PerFrameCB"]["gColor"] = float4(0, 1, 0, 1);

        mpScene->rasterize(pRenderContext, mpGraphicsState.get(), mpVars.get(), mpRasterState, mpRasterState);
    }
}
```

你还需要创建一个 `CMakeLists.txt` 来将新的 render pass 包含在构建中。

```cmake
add_renderpass(WireframePass)

target_sources(WireframePass PRIVATE
    WireframePass.cpp
    WireframePass.h
    WireframePass.3d.slang
)
target_copy_shaders(WireframePass RenderPasses/WireframePass)

target_source_group(WireframePass "RenderPasses")
```

还需要在父目录的 `CMakeList.txt` 中添加 `add_subdirectory(WireframePass)`。

使用 Render Graph Editor 创建一个仅包含此 pass 的 graph，然后在 Mogwai 中启动它，或者创建一个 Python 脚本。

```python
from falcor import *

def render_graph_WireframePass():
    g = RenderGraph('WireframePass')
    loadRenderPassLibrary('WireframePass.dll')
    Wireframe = createPass('WireframePass')
    g.addPass(WireframePass, 'WireframePass')
    g.markOutput('WireframePass.output')
    return g

WireframePass = render_graph_WireframePass()
try: m.addGraph(WireframePass)
except NameError: None
```

你应该会看到一个绿色屏幕，因为当前没有加载场景。通过 `File -> Load Scene` 加载场景，你应该会看到所选场景的线框。我们使用了 `media/Arcade/Arcade.pyscene`，效果如下：

![WireframePass](./images/wireframe-pass.png)
