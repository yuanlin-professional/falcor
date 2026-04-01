### [Index](../index.md) | [教程](./index.md) | 实现 Render Pass

--------

# 实现 Render Pass
现在你已经成功通过脚本在 Mogwai 中加载了 render graph，让我们来创建一个。Render graph 由多个组件 render pass 组成。本教程将重点介绍如何通过一个示例 pass 来创建 render pass，该示例 pass 将源纹理复制到目标纹理；graph 的创建和编辑将在下一节中介绍。

## 创建 Render Pass 库
所有 render pass 都位于 `Source/RenderPasses` 中，并编译为共享库（插件）。Render pass 库可以包含任意数量的 pass（插件类），但我们建议限制每个库中 pass 的数量。

运行 `tools/make_new_render_pass.bat <Name>` 来创建新的 render pass 库。这将创建一个包含新 render pass 库的子文件夹并调整构建脚本。

## 实现 Render Pass
如果你打开头文件和源文件，会注意到你的 pass 已经实现了一些从 `RenderPass` 继承的函数。还有其他可选的可继承函数，可以在 `RenderPass.h` 中找到。以下是所有 render pass 必须实现的函数：

### `create()`
此函数用于创建 pass，可以选择接受一个字典来初始化 pass 的值。

我们的示例 pass 不需要初始化任何内容，因此只需调用构造函数并返回包装在共享指针中的对象，这也是模板的默认行为。
```c++
ExampleBlitPass::SharedPtr ExampleBlitPass::create(RenderContext* pRenderContext, const Dictionary& dict)
{
    SharedPtr pPass = SharedPtr(new ExampleBlitPass);
    return pPass;
}
```

### `reflect()`
此函数描述 pass 所需的资源，并使用 `RenderPassReflection` 类为它们设置引用名称。这些资源可以标记为以下类型：
- `Input` 和 `Output` 含义不言自明。
- 同时标记 `Input` 和 `Output` 声明了一个透传资源：一个资源作为输入被要求，pass 将更新该资源，然后该资源也可以作为同名的输出被引用。
- `Internal` 告诉 render graph 系统分配一个在 pass 内部使用的资源。这目前与直接在 RenderPass 类中声明资源成员相同，但此 API 允许未来版本在幕后自动对资源内存进行别名和重用。

所有这些变体都有对应的辅助函数以简化使用：`addInput()`、`addOutput()`、`addInputOutput()`、`addInternal()`。

`ExampleBlitPass` 只需要一个输入和一个输出，不需要内部资源。我们创建 `RenderPassReflection`，添加一个输入和一个输出，然后返回。如果 pass 需要任何内部资源，也将在此处添加。

```c++
RenderPassReflection ExampleBlitPass::reflect(const CompileData& compileData)
{
    RenderPassReflection reflector;
    reflector.addInput("input", "the source texture");
    reflector.addOutput("output", "the destination texture");
    return reflector;
}
```

### `execute()`
此函数运行 pass，包含所有必需的渲染和/或计算操作以生成所需的输出。所有请求的资源都可以通过 `renderData` 使用在 `reflect()` 中分配的相同名称来访问。

`ExampleBlitPass` 将源纹理复制到目标纹理，分别通过 `renderData.getTexture("input")` 和 `renderData.getTexture("output")` 访问。`RenderContext` 已经实现了 blit 操作，它接受一个 `ShaderResourceView` 作为源和一个 `RenderTargetView` 作为目标。我们将这样使用它：
```c++
void ExampleBlitPass::execute(RenderContext* pRenderContext, const RenderData& renderData)
{
    const auto& pSrcTex = renderData.getTexture("input");
    const auto& pDstTex = renderData.getTexture("output");

    if (pSrcTex && pDstTex)
    {
        pRenderContext->blit(pSrcTex->getSRV(), pDstTex->getRTV());
    }
    else
    {
        logWarning("ExampleBlitPass::execute() - missing an input or output resource");
    }
}
```

## 注册 Render Pass

每个 render pass 库项目都包含一个 `registerPlugin()` 函数，用于注册库中实现的所有 render pass 类。
```c++
extern "C" FALCOR_API_EXPORT void registerPlugin(Falcor::PluginRegistry& registry)
{
    registry.registerClass<RenderPass, ExampleBlitPass>();
}
```

你可以通过调整头文件中的以下行来修改 render pass 库的描述：

```c++
FALCOR_PLUGIN_CLASS(ExampleBlitPass, "ExampleBlitPass", "Blits a texture into another texture.");
```

在本教程中，我们将不再深入讨论 render pass 及其实现的更多细节。更多信息可在[此处](../usage/render-passes.md)找到。
